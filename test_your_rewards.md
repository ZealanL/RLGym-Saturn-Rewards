# TEST. YOUR. REWARDS!

*What are the minimum and maximum ball heights while dribbling?*

*What is a reasonable minimum ball speed for a shot reward?*

*What is the minimum player-ball-goal 2D alignment during most air plays?*

The correct answer is *I don't know*.

There are some aspects of Rocket League where your intuition may suffice to craft good boundary values for rewards, but in most cases, you just don't know.

Even if you think you know, you could be very wrong.

All of my successful complex rewards are the result of *thorough* testing. I don't mean testing by adding them to your bot, I mean testing them against human players, usually you.

Here's how I come up with and test a reward:
1. Go into freeplay, load [TAS plugin](https://bakkesplugins.com/plugins/view/351)
2. Make a few recordings: A couple for the desired behavior, and a couple for similar but undesired behaviors (if you can't perform the desired behavior normally, that's fine, use the TAS tools to help you)
3. Play those recordings, and print out a bunch of relevant physics or car data values (car height, position relative to the ball, velocity relative to the ball, direction to ball, etc.)
4. Determine what conditions of those values are true for the desired behavior, and false for the similar undesired behaviors
5. Write a reward based on those values
6. Test the reward on those recordings, tune as needed
7. Add the reward to the bot

To record these values and test your reward in-game, you have three main options:
1. Use a custom RLBot script (probably the easiest option for most bot creators)
2. Use a custom bakkesmod plugin (I recommend this option if you are writing your ML bot in C++, like me)
3. Save a replay of the different TAS recordings, and use a replay parsing tool to run the rewards on those replay states

If you do not test your complex rewards, expect to waste countless hours and billions of steps working on and training with rewards that simply don't work very well.
I think many people simply shy away from complex rewards because they do not test their rewards this thoroughly, but complex rewards are extremely powerful and useful, as I have hopefully proven with Saturn.

[<-- Back to Table of Contents](README.md)