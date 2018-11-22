---
layout: post
title: The story of a un-bindable string
gh-repo: saguar/saguar-blog-code
tags: [binding, development, immutable, issue, mvvm, problem, string, UWP, wpf, xaml]
---

##Prologue

Few days ago I was working on a simple WPF application using all the best practices I’ve learned during these years: 
<ul>
  <li>
    Singleton
  </li>
    Code grouping
  <li>
    Inversion of Control
  </li>
  <li>
    Mvvm pattern
  </li>
  <li>
    Properties binding
  </li>
</ul>
but at some point I faced with an issue that made me crazy! 

##The problem

In my simple application I had to load a list of words from a file and then show them into a simple list, giving the possibility to the user to change one of these words by clicking a “Edit” button. I know… You are thinking “come on! this is the simplest exercise I’ve ever done using Mvvm Pattern! What’s wrong with this?” Okay, let me explain it showing you some screenshots: This is the code I wrote in XAML page for the list 
 
As you may notice I’ve used a simple ListView with a binded ItemSource (I’m not showing you the details of the Mvvm implementation since it doesn’t matter) and I’ve defined a simple DataTemplate for the list items. 
Note: since the MyStringList object is an ObservableCollection<string> I binded the Text property of my TextBox items to the object itself (.) with a Mode=Two-way behavior. Nothing special, right? Okay… this is the result when I run the application:

My first thoughts: 

<ul>
    <li> is the list correctly initialized?</li>
    <li> is the binding process working? </li>
    <li> am I using a “raisable” object? </li>
</ul>

So I started debugging the View Model initialization to be sure that the ObservableCollection was correctly instantiated and then I checked in the View’s code behind that the DataContext property was correctly setted. All was fine… To be sure about the binding process I decided to remove the Mode=TwoWay from the Binding definition, just to see if the values were at least showed in read-only mode.

##Eureka!

##The Solution

##Conclusion