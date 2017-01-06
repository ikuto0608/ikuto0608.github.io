---
layout:     post
title:      Marble solitaire solver
date:       2017-01-06 02:34:29
summary:    This is how I solved Marble solitaire with ruby.
categories: Ruby
---

It was happy new year! I was solving Marble solitaire during this Christmas holidays. I'm going to explain how I solved Marble solitaire with ruby.


### What is Marble solitaire?

Here's a image of Marble solitaire.
![marble](https://github.com/ikuto0608/marble_game_solver/blob/master/images/sample1.jpg?raw=true)

First of all, you remove center marble, then start the game.
You only can across marbles into an empty space.
Remove the marble you just jumped over.
Then when you get only one marble, you'll complete the game.
Pretty simple game hey? But honestly, I couldn't solve it by my self. So I changed my mind to solve it with programming!


## First approach

I made a program which tries to move every pattern until there is left one marble in the game.
But there was a problem, I executed the program then after 70 hours, the program was still working! I realized there are uncountable patterns to move marbles. So I modified my code.

{% highlight ruby lineanchors %}
# Snippet
def play_game
  filled_coordinates = @current_field_hash.select{|key, value| value == FILLED }
                                          .keys
  filled_coordinates.each do |coordinate|
    field_hash = @current_field_hash.dup
    record = @record.dup

    if can_jump_right?([coordinate[0], coordinate[1]])
      jump_right([coordinate[0], coordinate[1]])
      play_game
      break if complete_game?
    end

    @current_field_hash = field_hash.dup
    @record = record.dup

    if can_jump_down?([coordinate[0], coordinate[1]])
      jump_down([coordinate[0], coordinate[1]])
      play_game
      break if complete_game?
    end

    @current_field_hash = field_hash.dup
    @record = record.dup

    if can_jump_left?([coordinate[0], coordinate[1]])
      jump_left([coordinate[0], coordinate[1]])
      play_game
      break if complete_game?
    end

    @current_field_hash = field_hash.dup
    @record = record.dup

    if can_jump_up?([coordinate[0], coordinate[1]])
      jump_up([coordinate[0], coordinate[1]])
      play_game
      break if complete_game?
    end

  end
end


puts "Start at: #{Time.now}"
initialize_field
play_game
puts "Finish at: #{Time.now}"
@record.each_with_index do |r, index|
  puts "#{index + 1},#{r}"
end
{% endhighlight %}

## Second approach

First time, I didn't record each case of field. So even if the program get same case with before, it won't skip it. So my second approach is that records each case to database. (I actually got the idea from my friend. Thanks million.)
Now the program solved it! It took about 10 minutes.

## Third approach

I solved the game, then I literally moved marbles following my program did. Yeah, it's solved!
But yeah, it takes 10 minutes to solve it. So long...
Then I improved it, it takes 2 minutes finally!
As you saw, the field of game has symmetry. The shape is same as rotated 90, 180 and 270 degrees. And invert it as well.
It means you can zip patterns to one over eight! That's what I did.
Here is my final code. Let me know if there is more faster way!
![https://github.com/ikuto0608/marble_game_solver](https://github.com/ikuto0608/marble_game_solver)
---
