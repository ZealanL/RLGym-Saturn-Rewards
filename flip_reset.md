# Flip Reset Rewards

Saturn has 3 flip reset rewards:
- Continuous flip reset reward
- Flip reset event reward
- Reset shot reward

## Continuous flip reset reward

This is a [continuous reward](continuous_reward.md) for going towards getting a reset.

First, there are several requirements for this reward to be active:
- Player must not already have a flip
- Player must be above a minimum reset height
- Player's upwards direction must be facing down
- Player must be near to the ball
- Player must be getting closer to the ball (velocity relative to the ball's velocity is towards the direction of the ball)

All of these checks can be triggered by a bot that is already air dribbling with air roll, or in an occasionally upside-down orientation.

After these checks are passed, we take 3 measurements to determine the proximity to getting a reset:
1. Relative speed towards ball (velocity relative to the ball's velocity, in the direction towards the ball)
2. Downward alignment (how aligned the car's downward direction is to the direction towards the ball)
3. Distance to ball

All of these three measurements are normalized from 0-1, using general minimums and maximums for each measurement.
The overall reset proximity is the minimum of these 3 values.

The bot is then rewarded using my continuous-closeness method, the closeness being the reset proximity.

## Flip reset event reward

Now, this one might seem very simple and easy.
Just reward the bot for being on the ground with the ball directly below, right? Well no, it's not that simple.

Now sure, that simple reward logic will detect some resets, but it has 3 major flaws:
1. The reward will activate for every step the player is on the ball, meaning the bot can stay on the ball to farm the reward
2. The reward will activate for sitting atop the ball on the ground
4. The reward will not activate if the reset happens between two steps (some resets only activate `on_ground` for as little as a single tick)

Instead of rewarding being on the ball at the current step, we should reward the bot for getting a flip since last step, i.e. `player.has_flip and !last_has_flip`.
We can then check to see if we are very close to the ball, and the ball is directly under us. 
Make sure to test to find the optimal range of values for this; the maximum misalignment that can result in a reset is much larger than I thought.

Once you have both of these checks, your reward should only activate if the bot has got a reset within the last step and didn't already have one.
Note that `on_ground` does not need to be checked anywhere in the reward.

Some additional conditions should be added, as to not reward lazy resets as the ball is rolling across the ground and such. 
I recommend adding a minimum height requirement, as well as requiring the car's up vector to have a negative z component to encourage upside-down resets (you can disable this check once your bot knows how to reset).

## Reset shot reward

To reward shooting with a reset, we need to know if the player got a reset. 
This is different from having a reset, as a strong reset shot will use the reset to hit the ball, and thus actually make contact with the ball without a reset.

You might think that, in order to determine if we had a reset before shooting, we will need to track each player's reset event during that player's air time.
But fear not! We actually don't need any persistent information to know this.

Instead, we can use the simple fact that players with resets have never jumped, even after using the reset.
A player that has gotten a reset, and has been in the air since, will satisfy the condition `!on_ground and !has_jumped`.
To determine if the player has used the reset, we can check `has_flip`.

At the time of writing, `rlgym_sim` does not have `has_jumped` in the player data (unlike my C++ translation), but it is present in the RocketSim car state, as well as Rocket League.
If you don't have it in your player data, I highly recommend adding it, along with other extremely useful and convenient state variables like `is_flipping`, `air_time_since_jump`, `wheels_in_contact`, etc.

My reset shot reward only activates if `got_reset` (as described above) and `!has_flip`, meaning the player has used their flip from the reset.

To encourage keeping a reset and continuing the air play, or delaying a reset shot to line up for more power or a flick, I add a sizable bonus to the reward for the amount of time since the reset was obtained.
To determine this, you *will* need to track when the player got a reset. Unfortunately, `air_time_since_jump` won't work because a player with a reset has never jumped.

The whole reward is scaled by the force of the hit, along with the resulting speed of the ball, to encourage a stronger shot.

[<-- Back to Table of Contents](README.md)
