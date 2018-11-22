---
layout: post
title: The story of a un-bindable string
gh-repo: saguar/saguar-blog-code
tags: [binding, development, immutable, issue, mvvm, problem, string, UWP, wpf, xaml]
---

## Prologue

Few days ago I was working on a simple WPF application using all the best practices I’ve learned during these years: 
<ul>
    <li>Singleton</li>
    <li>Code grouping</li>
    <li>Inversion of Control</li>
    <li>Mvvm pattern</li>
    <li>Properties binding</li>
</ul>
but at some point I faced with an issue that made me crazy! 

## The problem

In my simple application I had to load a list of words from a file and then show them into a simple list, giving the possibility to the user to change one of these words by clicking a “Edit” button. I know… You are thinking “come on! this is the simplest exercise I’ve ever done using Mvvm Pattern! What’s wrong with this?” Okay, let me explain it showing you some screenshots: This is the code I wrote in XAML page for the list 

![img 0](/img/bindable_string_problem_0.png)

As you may notice I’ve used a simple ListView with a binded ItemSource (I’m not showing you the details of the Mvvm implementation since it doesn’t matter) and I’ve defined a simple DataTemplate for the list items. 

Note: since the **MyStringList** object is an **ObservableCollection<string>** I binded the Text property of my **TextBox** items to the object itself (.) with a **Mode=Two-way** behavior. Nothing special, right? Okay… this is the result when I run the application:

![img 1](/img/bindable_string_problem_1.png)

My first thoughts: 

<ul>
    <li> is the list correctly initialized?</li>
    <li> is the binding process working? </li>
    <li> am I using a “raisable” object? </li>
</ul>

So I started **debugging** the View Model initialization to be sure that the **ObservableCollection** was correctly instantiated and then I checked in the View’s code behind that the **DataContext** property was correctly setted. All was fine… To be sure about the binding process I decided to remove the **Mode=TwoWay** from the Binding definition, just to see if the values were at least showed in read-only mode.

![img 2](/img/bindable_string_problem_2.png)

And… I was totally confused! What's wrong with that code? Why binding a string is not working with the TwoWay mode?

## Eureka!

After a quick chat with a colleague he suggested to me to encapsulate that string in a container object, and bind the property to **Container.Value** instead of the value itself, because the binding problem could be related to the code notation:
~~~
{Binding Path=., Mode=TwoWay}
~~~

As a result of this test, the bi-directional binding was now working… for an unexplainable reason:
<ul>
    <li>binding the Text property to a string variable **DOES NOT WORK in TwoWay**</li>
    <li>binding the Text property to a **Value** field (of string type) inside a container object **WORKS in TwoWay**</li>
</ul>
Honestly I have no idea if it’s a bug/problem or it’s just something wrong with my approach, by the way I decided to define a **BindableObject** class in order to bypass this issue.

## The Solution

Since this issue made me mad for 45 minutes, I decided to implement an efficient and reusable solution! 
![img 3](/img/bindable_string_problem_3.png)

As a result of this experience, I’ve coded a **BindableObject<T>** container class which can be used with any property type (simple or complex), and since the object itself implements the **INotifyPropertyChanged** interface, the contained property will raise a changed event for binding. 

So the new line of code for binding a value to the Text property would be:
~~~
<TextBox Text="{Binding Path=Value, Mode=TwoWay}" />
~~~
In addition to the **Value** property, which is the core objective of this utility class, I’ve also added a **OldValue** property (which is automatically set when the Value property changes) and a **FormattedValue** property which would return a formatted version of the Value property itself (the formatting method must be implemented and customized). 

## Conclusion

I have uploaded the code of this helper object to [my Github repository](https://github.com/saguar/saguar-blog-code/) and additionally I’ve created a Nuget package for this and all the future helpers/utilities I usually use for Mvvm projects.

*Update (16/03/2017)
As suggested by Tyrrrz in a comment, this issue could be related to the immutable behavior of the string type (as also described in the official documentation), so whereas binding a single string property to a TextBox works (probably because after the RaisePropertyChanged event the Text property of the component uses the new string object reference) if you define a List of strings then the TextBoxe(s) inside the ListView would always refer to the original string objects (whose value is not changing).*
