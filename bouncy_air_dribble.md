# Bouncy Air Dribble Reward

Saturn's bouncy air dribble reward is the most complex and well-tested mechanic reward in the bot.

Though extensive testing, I was able to define a set of conditions that are only all true during the entirety of a good, bouncy air dribble towards the enemy's net.

If you don't know why it has to be bouncy, it is because bouncy air dribbles are stronger in general. 

Most air dribbling ML bots I had seen when making Saturn had gluey contact, where there is little to no space between the nose of the car and the ball.
This air dribble looks cool, and is more difficult than a bouncy air dribble, but in most cases, is worse. You don't have any space to change the direction of a ball, nor go for a reset. 

Before we get into defining an air dribble towards the opponents net, let's define how we can detect this bounciness.

At any point of the ball bouncing above our car, one of these 2 conditions is met:
1. The ball is at least a certain distance away
2. The magnitude of our velocity relative to the ball's velocity (`player.vel - ball.vel`), exceeds some threshold

I initially started Saturn with a fairly high value for both thresholds, and then gradually reduced both values as the bot's air play became more advanced, as to not be limiting.

Now that we know what bouncy is, what's an air dribble?

I have come up with these conditions, which are required for a given player-ball state to be an air dribble.
I have designed these conditions to not limit the wide range of air dribble variety, but also to guarantee that any bot meeting all these conditions must be air dribbling towards the opponent's net.

1. The ball must be above a certain height, OR the player and ball are gaining height quickly.
2. The player is not too far from the ball.
3. The player has enough boost. You can determine this minimum boost amount however you want. Personally, I chose `15 * (dist_to_goal_2d / 5000)**1.75` as my minimum.
4. The player-to-ball direction's Z component (`normalize(ball.pos - player.pos).z`) is at least some minimum.
5. The player and ball are both moving towards the goal at some minimum speed.
6. The 2D player-to-ball direction is near the 2D ball-to-goal direction.
7. The height of the ball when it will reach the enemy goal's Y is not too far above crossbar (we don't want the bot air dribbling into the backboard).

Another additional condition that seems quite helpful is a near-goal shutoff condition.
There is a wide variety of effective plays that can be made once the air dribble is near the goal, some of which do not satisfy these conditions.
To not discourage the exploration and development of these various plays, I have the reward disable once the ball will reach the enemy goal's Y in under 2 seconds.

[<-- Back to Table of Contents](README.md)
