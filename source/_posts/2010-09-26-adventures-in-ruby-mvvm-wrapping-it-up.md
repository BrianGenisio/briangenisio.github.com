---
title: Adventures in Ruby MVVM – Wrapping it up
date: 2010-09-26 00:52:27
layout: post
category: Software
tags: [MVVM, Ruby]
permalink: /archives/2010/09/26/adventures-in-ruby-mvvm-wrapping-it-up/
---

[More Adventures in MVVM](/archives/2009/05/22/adventures-in-mvvm-model-view-viewmodel/) 

[Source code for the RubyMVVM playground on BitBucket](http://bitbucket.org/briangenisio/rubyvm/src)

It has been quite a while since I blogged.&#160; Actually, I was just about to sit down to write this blog post about 7 weeks ago when my wife went into labor.&#160; Soon afterwards, she gave birth to our second child -- a son named [Eli Hecker Genisio](http://maia.genisio.org/2010/08/elis-birth.html).&#160; Needless to say, I have been neglecting blog for a while.

Previous to that, I had gotten to a place where I felt like I was ready to wrap up my experiments in using Ruby for ViewModels and Models.&#160; Here is what I would have written:

### What Worked, What Didn’t?

Using Ruby as the primary logic and model platform for MVVM (Presenter Model) style apps in WPF was a ton of fun.&#160; It gave me the chance to learn Ruby pretty well (I’m still a hack, but who isn’t?) and got to use many of the more advanced topics such as meta programming and mix-ins.&#160; My ViewModel code was succinct and my model code was even more trivial once I employed the [HTTParty](http://httparty.rubyforge.org/) gem.&#160; What was most fun (and likely my biggest take-away) was how wonderful writing tests using [rspec](http://rspec.info/) was.&#160; All of my tests were written using rspec and I think I might start writing my C# tests using rspec in the future.&#160; 

What didn’t work: The general coding workflow.&#160; Since there is no tooling support for IronRuby in VisualStudio 2010, it became a disjoined development effort.&#160; This was not completely bad, but things like debugging and deployment became difficult.&#160; I cringe when I think about deploying ruby gems with a Silverlight app. In addition ,the integration between .Net and IronRuby at runtime still needs work.&#160; For instance, defining anything but a trivial property in Ruby did not translate well into the .Net side.&#160; Also, WPF had difficulty binding to ruby strings.&#160; Some controls worked well, but some completely failed.&#160; I ended up writing a ValueConverter in WPF that called ToString() on the Ruby string in order to bind properly.&#160; Silverlight can’t even try to bind to a dynamic property, so that was even more difficult.

In all, I left feeling like the “goods” and “bads” canceled themselves out and I was stuck asking myself: “Why would I want to do this for real applications?”.&#160; My answer is: “Until there is good tooling support for IronRuby in Visual Studio (don’t hold your breath), this story probably doesn’t have any legs”.

### The Playground – A Twitter Search App

![RubyMVVM_Twitter](/images/RubyMVVM_Twitter.png)

With that, I’d like to document my playground in case anyone feels like running with it.&#160; I created a WPF Twitter Search application (the 2010 hello world) and hoste d the code on [BitBucket](http://bitbucket.org/briangenisio/rubyvm/src).&#160; Once I built all of the [mix-ins](http://bitbucket.org/briangenisio/rubyvm/src/tip/IronRubyMVVM/RubyVM/) necessary to support automatic property notification and convention-based command creation (amongst others), I came up with a ViewModel which is (mostly) void of .Net integration issues that looks like this:

```ruby
 class TwitterViewModel
    include ViewModelSupport
   
    declare_notifiable :search_text, :responses
   
    def responses
      @responses ||= NotifiableArray.new
    end
   
    def execute_search
      responses.clear
   
      Twitter.search(@search_text)["results"].each do |tweet| 
        responses.push(TweetViewModel.new(tweet)) 
      end
    end
   
    map_commands
  end
```
  

1. Notice that the very first thing I do is add the **ViewModelSupport** mixin.&#160; This provides the support for the magic that happens under the hood.&#160; 

2. Immediately following, I use the **declare_notifiable** directive.&#160; This will create a property that will notify on change, using **INotifyPropertyChanged **in the .Net world.&#160; This was included with the mix-in.

3. The **responses** method (read-only property in .Net) is a **NotifiableArray**. This is a Ruby array I built that will send collection changed events to .Net. 

4. The **execute_search** method uses the **execute_** convention to generate an **ICommand** property named **search** that can be bound to in the view.&#160; This is what gets executed when the user presses the “Search Twitter” button.&#160; It goes off to Twitter (**Twitter.search** to be explained later) to search for the text entered into the **search_text** property.&#160; For each tweet that comes back, wrap it in a **TweetViewModel** (explained later) and put it into the **responses** collection.&#160; The View will update appropriately.

5. The **map_commands** line is a class method that tells Ruby to go map any methods named **execute_*** to command properties (*) that can be bound to by the view.

As for the individual **TweetViewModel**, it looks like this:
```ruby
class TweetViewModel
   include HashExposer
   expose :text, :profile_image_url, :created_at, :from_user
end
```

It uses **HashExposer** which is a mix-in I wrote that will find any items in the hash with the key declared with **expose** as a property.&#160; It is a very simple wrapper to allow for binding in the WPF view down to the individual tweet properties found in the hash.&#160; The hash that is passed in in the initializer is what these properties map to.

Finally, here is what the **Twitter** service looks like:
```ruby
class Twitter
  include HTTParty

  base_uri ‘search.twitter.com’
  format :json

  def self.search(query)
    get(‘/search.json’, :query =>; {:q => query})
  end
end
```

By using the [HTTParty](http://httparty.rubyforge.org/) gem, I get to define what **Twitter.search(text)** looks like in a minimal way.&#160; The response of **search** is a parsed hash which I wrap with the **TweetViewModel**.&#160; This is an AMAZINGLY simple way to consume web services in Ruby.&#160; I was shocked to see how easy it was.&#160; 

That is everything on the Ruby side!&#160; All of the application-specific ruby code has been shown in this blog post.&#160; By using the **ViewModelSupport** mix-in (found in the source code), the rest of the .Net integration is abstracted away.&#160; With this alone, I felt like I was successful.&#160; 

Next, I’d like to show the WPF side of the application.&#160; This is what the view looks like:
```xml
 ﻿<UserControl
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    xmlns:local="clr-namespace:IronRubyMVVM"
    xmlns:SampleData="clr-namespace:Expression.Blend.SampleData.Tweets"
    mc:Ignorable="d"
    x:Class="IronRubyMVVM.TwitterView"
    x:Name="UserControl" d:DesignHeight="651.96" d:DesignWidth="669"
    >
 
    <UserControl.Resources>
        <SampleData:Tweets x:Key="Tweets" d:IsDataSource="True"/>
        <local:StringConverter x:Key="ToString" />
        <DataTemplate x:Key="responsesItemTemplate1">
            <Border BorderThickness="2" CornerRadius="4" BorderBrush="Black" Margin="3">
                <Grid>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="*" />
                    </Grid.ColumnDefinitions>
                    <Grid.Background>
                        <LinearGradientBrush EndPoint="0.5,1" StartPoint="0.5,0">
                            <GradientStop Color="#FFD1EBFB" Offset="0.694"/>
                            <GradientStop Color="White" Offset="0.116"/>
                        </LinearGradientBrush>
                    </Grid.Background>
                    <StackPanel Grid.Column="0" Margin="2">
                        <Border BorderBrush="Black" BorderThickness="3" Height="100" Width="100" CornerRadius="5" Margin="3">
                            <Image Source="{Binding profile_image_url, Converter={StaticResource ToString}}" Margin="7" />
                        </Border>
                        <TextBlock Text="{Binding from_user}" HorizontalAlignment="Center" />
                        <TextBlock Text="{Binding created_at}" HorizontalAlignment="Center" />
                    </StackPanel>
                    <TextBlock Grid.Column="1" Text="{Binding text}" VerticalAlignment="Top" TextWrapping="Wrap" Margin="8,30,0,0" FontSize="18.667" />
                </Grid>
            </Border>
        </DataTemplate>
        <ItemsPanelTemplate x:Key="ItemsPanelTemplate1">
            <StackPanel IsItemsHost="True" />
        </ItemsPanelTemplate>
    </UserControl.Resources>
    <UserControl.Background>
        <LinearGradientBrush EndPoint="0.5,1" StartPoint="0.5,0">
            <GradientStop Color="#FFD6D6D6" Offset="0"/>
            <GradientStop Color="White" Offset="1"/>
        </LinearGradientBrush>
    </UserControl.Background>
 
    <UserControl.DataContext>
        <Binding Path="TwitterViewModel" Source="{StaticResource VMLocator}"/>
    </UserControl.DataContext>
 
    <Grid x:Name="LayoutRoot">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>
        <StackPanel Orientation="Horizontal" Grid.Row="0">
            <TextBox Width="200" VerticalAlignment="Top" Text="{Binding search_text, Mode=TwoWay, UpdateSourceTrigger=PropertyChanged}" Margin="5,0" />
            <Button Content="Search Twitter" VerticalAlignment="Top" Command="{Binding search}" />
        </StackPanel>
        <ItemsControl Grid.Row="1" ItemsSource="{Binding responses}" ItemTemplate="{DynamicResource responsesItemTemplate1}" d:DataContext="{Binding Source={StaticResource Tweets}}" HorizontalContentAlignment="Stretch" ItemsPanel="{DynamicResource ItemsPanelTemplate1}">
            <ItemsControl.Template>
                <ControlTemplate>
                    <ScrollViewer x:Name="ScrollViewer" Padding="{TemplateBinding Padding}">
                        <ItemsPresenter />
                    </ScrollViewer>
                </ControlTemplate>
            </ItemsControl.Template>
        </ItemsControl>
    </Grid>
</UserControl>
```

Just as in any MVVM application, all of the connections between the View and the ViewModel happen through binding.&#160; This is no exception.

The only C# in this entire application are in the [bootstrapping of the ruby classes](/archives/2010/07/07/adventures-in-ruby-mvvm-bootstrapping-ruby/) (**ViewModelLocator**) and the **StringConverter** which helps bind the view to Ruby strings.

The code for these support classes can be found in the [source code on BitBucket](http://bitbucket.org/briangenisio/rubyvm/src).

This puts to rest my Ruby-based MVVM experiments.&#160; I learned a ton and I think there is a good foundation here for improvement if anyone wants to run with it. 

For me, I will be moving on to some posts about Flex and ActionScript – another learning project for me to experiment with.&#160; Stay tuned :)