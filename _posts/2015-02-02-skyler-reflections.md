---
title:  "Reflections for Skyler: Creating an AI Player"
---

I really liked how Skyler created an AI Player so I decided to describe it for one of my reflections.


As I see it, a Player object stores the name of the player, their score, and prompts them for moves (I'm curious if the decision to prompt the player's moves here was made after deciding to implement an AI option or before. If before, why).

To explain how the AI Player works, it's necessary to explain the Player object first.

New Player objects are created at the initialization of a Game object; that's where the user decides the names for their Players.

{% highlight ruby %}
  def initialize(*players)
    array = Array.new
    players.each do |name|
      if name.end_with?("AI")
        array << AI_Player.new(name)
      else
        array << Player.new(name)
      end
    end
    @p1 = array[0]
    @p2 = array[1]
    @rules = nil
  end
{% endhighlight %}

Once a Player is created, their `acquire_move` method is looped through:

{% highlight ruby %}
  def acquire_move(ruleset)
    @move = ""
    while ruleset.valid_moves_list.include?(@move) == false do
      ruleset.valid_moves_list.each do |x|
        puts x.capitalize
      end
      puts "#{@name}, choose your move: "
      @move = gets.chomp.downcase
    end
    puts "#{@name} chose #{@move.capitalize}."
  end
{% endhighlight %}

It is not clear to me where in the code that is being looped and how you determine how many times to loop. It was clear in your video that the program does it correctly, but it's not so clear in the code (at least to me). Maybe it's the nested `new_match`, `new_game`, and `determine_winner` methods? Is it possible to combine these? Do you ever use them on their own in a meaningful way? Anyway, this loop prompts the Player for valid moves.

The AI Player is distinguished from a normal Player just in that it has a key attached to the end of their name "AI." While I think this assumes a lot of knowledge on the user's part as it is written, the code could easily be tinkered to prevent against user error. It was a smart solution for the project. The AI player differs in that, instead of `get`-ting moves from the user, it generates them. This is done by a random generator from an array of numbers that correspond to each move array index + 1. Choosing a number with the generator, modifying it slightly by subtracting 1, and then calling the move indexed results in a random move generator. (I thought this was really slick but maybe more complicated than it needed to be. Why not use .sample on the array of permitted moves?) The `aquire_move` method for the AI Player then puts the generated move out, instead of gathering from the prompt seen in the normal Player object.

{% highlight ruby %}
  def acquire_move(ruleset)
    prng = Random.new
    number_of_moves = ruleset.valid_moves_list.length
    x = prng.rand(number_of_moves)
    @move = ruleset.valid_moves_list[x-1]
    puts "#{@name} chose #{@move.capitalize}."
  end
{% endhighlight %}