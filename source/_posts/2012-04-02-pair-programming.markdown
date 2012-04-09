---
layout: post
title: "Pair Programming"
date: 2012-04-02 02:46
comments: true
categories: ["programming"]
---
I'm relatively new to "pair programming", but there seem to be a range of opinions on whether it is 
a useful way to get things done. I'm of the opinion that it can be good, and so far I've had several
sessions where I felt like I was much more productive in a pair, than I would have been alone. I don't
claim to be an expert in the field, so a lot of this is my opinion or speculation--feel free to leave
comments if you disagree [or agree]. In my particular case, I've done mostly remote pair work, and 
I think that it's much more useful to be on the phone and having instant feedback in audio, instead
of alt-tabbing and typing to communicate.
<!--more-->
The wikipedia page refers to the concepts of Navigator and Driver. The Driver focuses on completing 
the agreed upon task while the Navigator ponders on how to make it better, and watches for issues with the
way the Driver is doing the implementation. 

If you don't like chess, skip the rest of this paragraph. 
It's kind of like if you were to break up a chess game into
tactics (short-lived maneuvers), and position (long-term strategy) between two expert chess players,
and have them continually discuss what they're thinking about as the game moves along. The Driver would
be focused on the tactics, looking at what the other player just did that might open him/her up to 
some badness. The Navigator would be there to make sure the Driver didn't overlook a potential tactic, 
meanwhile refining the overall defense or attack, which would be the deciding factor for moves where 
no awesome tactics can be taken advantage of. The equivalent non-pair scenario would be having two
expert chess players looking at the board separately, with more limited communication, and then breaking
it up on a move by move basis, like "I'll look at e4, you look at d4", and then they come back together
and make a decision on which to do. Ok, maybe that's a bit of a stretch, but hopefully you see where I'm
coming from.

First, I'll say that there is a comfort level that you need to have between people in a pair. You need
to be able to say you don't know how to do something, so you're going to try to figure it out (via whatever means you planned to figure it out). If you
aren't willing to be that open about your work, then the pair session is going to be wasted, in my opinion.

Second, both people need to be actively involved for it to work better than a single programmer. This is
probably obvious, but there are several ways that someone can be not actively involved, aside from the
more obvious ones, like falling asleep in their chair while watching.

If the skill level relating to the task is drastically different (programmers referred to as newbie and expert form now on), 
then the pair productivity is going to suffer. If you're using pair programming
as a teaching tool (which I think is a possible use case), then you can expect the pair to work slower than the expert individual. As a Driver, the expert
has to basically tell the newbie what to do. This can get to be frustrating for the expert, but if they're
able to stay cool, the newbie can hopefully learn as it's getting forced down their throat. If the newbie skill is so
low that you have to spell things out, like 
"take out the second part of that if statement--everything after the OR--the two pipes--the two vertical bars--yeah, that--and everything after that--wait, except the closing paren"
then it's going to be pretty slow going (time may be better spent sending the newbie to a programming 101 class), although they will probably eventually get
to understand what's going on and be able to work on their own.
If the expert is Driving, they would need to be explaining everything
they do, and the newbie would need to ask questions if they don't understand something. The end design in this teaching
case is probably going to be about what the expert would have done him/herself, although sometimes just explaining
something to someone makes you consider maintainability, usability, etc., in a deeper way than you would just
trying to get your tests to pass. 

I think I've bitten off more than I can chew in a single post. Maybe I'll post again, but for now I've set up a
<a href="http://wrangl.com/pair-programming-vs-not-pair-programming">wrangl argument</a> to try to get some pros and cons listed for pair programming.
