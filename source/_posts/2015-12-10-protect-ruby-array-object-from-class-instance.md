---
title: Protect Ruby Array Object from Class Instance
abbrlink: 9a371ac
date: 2013-12-03 19:44:54
tags:
---
``` ruby
class PlayingCard
  SUITS = %w{ clubs diamonds hearts spades }
  RANKS = %w{ 2 3 4 5 6 7 8 9 10 J Q K A }

  class Deck
    def cards
      @cards.to_enum
    end

    def initialize(n=1)
      @cards = []
      SUITS.cycle(n) do |s|
        RANKS.cycle(1) do |r|
          @cards << "#{r} of #{s}"
        end
      end
    end
  end
end

deck = PlayingCard::Deck.new
deck.cards << "JOKER!!" #=> FAIL!!
```

Playing around with this, knowing without the line `@cards.to_enum`, I could actually input a new item in the array with ease, but sometimes we don't want that. Therefore, given the enumerator like `to_enum` will allow for iterations through the array without absorbing changes.
