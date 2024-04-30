# Making Effective Continuous Rewards

Getting bots to discover things is difficult, so I've found you can achieve rapid discovery of advanced behavior, so long as you create a well-rounded and non-limiting continuous reward for that behavior.

**Continuous** means the reward can happen for multiple ticks in a row, instead of just triggering when the desired behavior occurs.

Effective continuous rewards reward the bot for *going towards* the desired behavior, not just executing it.

## Solving the paradoxical nature of continuous rewards

Let's say, for example, you want your bot to learn to flip reset. 
You've tried adding an event reward, but it simply happens too rarely for the bot to be reinforced by it.

A seemingly good reward is to create some closeness function to getting a reset, and reward the bot based on that closeness function
```py
def reset_closeness(car, ball): # Returns from 0-1
	...
	
reward = reset_closeness(car, ball)
```

Great! But depending on what the behavior is, this might not work very well in practice.

Since the bot isn't already getting resets, the `reset_closeness()` output will always be quite low.

If `reset_closeness()` only outputs up to 0.1 with your bot's current gameplay, you will need a 10x stronger reward to influence the bot's learning.

If you make the reward very strong, the bot will learn to get a higher `reset_closeness()`. 
Then, once the maximum `reset_closeness()` becomes something higher, like 0.5, your reward is now 50x stronger, and is probably too strong.

Another problem with this reward is that we are rewarding the bot for being *close* to getting a reset. 
Thus, the optimal way to maximize this reward is to stay close to getting a reset, but never actually get one.

There is a simple solution for all of these problems: Instead of rewarding being close to a reset, reward getting *closer*.

We can implement this change like so:

```py
closeness = reset_closeness(car, ball)
reward = max(0, closeness - last_closeness)
last_closeness = closeness
```

Now, we are rewarding the bot for getting closer to getting a reset, based on how fast it is getting closer.

This does not incentivise getting the reset slowly, as the reward scales with the rate of increasing closeness.

You may be tempted to remove `max()`, allowing the reward to go negative, to prevent the bot from being able to farm the reward by repeatedly jittering closer and further. 
However, in practice, I have never had this problem, and removing the `max()` can hurt the development of other behaviors that might partially trigger the reward.
A strong event reward for executing the desired behavior, alongside this continuous reward, should incentivize against this.

This model of continuous reward is what Saturn uses for several different mechanics, and it has been incredibly effective, as well as causing very rapid learning of the desired behavior.

[<-- Back to Table of Contents](README.md)