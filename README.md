just-ryd
========
One of my Coursera Fundamentals of Computing projects. 
This one is the classic game "Pong" written in CodeSkulpter using simplegui. 
Fun and interesting fo an old fart like me!


# Implementation of classic arcade game Pong
import simplegui
import random

# initialize globals - pos and vel encode vertical info for paddles
WIDTH = 600
HEIGHT = 400       
BALL_RADIUS = 20
PAD_WIDTH = 8
PAD_HEIGHT = 80
HALF_PAD_WIDTH = PAD_WIDTH / 2
HALF_PAD_HEIGHT = PAD_HEIGHT / 2
paddle1_pos = HEIGHT / 2
paddle2_pos = HEIGHT / 2
paddle1_vel = 0
paddle2_vel = 0
RIGHT = True
LEFT = False
score1 = 0
score2 = 0

# initialize ball_pos and ball_vel for new bal in middle of table
# if direction is RIGHT, the ball's velocity is upper right, else upper left
def spawn_ball(direction):
    global ball_pos, ball_vel # these are vectors stored as lists
    ball_pos = [WIDTH / 2, HEIGHT / 2]
    # assigns variables to random velocity at start / restart
    a = random.randrange(2,5)
    b = random.randrange(1,4)
    if direction == RIGHT:
        ball_vel = [a,-b]
    else:
        ball_vel = [-a,-b]
 
# define event handlers
def new_game():
    global paddle1_pos, paddle2_pos, paddle1_vel, paddle2_vel  # these are numbers
    global score1, score2  # these are ints
    score1 = 0
    score2 = 0
    spawn_ball(RIGHT)
 
def draw(canvas):
    global score1, score2, paddle1_pos, paddle2_pos, ball_pos, ball_vel, paddle1_vel, paddle2_vel
    # draw mid line and gutters
    canvas.draw_line([WIDTH / 2, 0],[WIDTH / 2, HEIGHT], 1, "White")
    canvas.draw_line([PAD_WIDTH, 0],[PAD_WIDTH, HEIGHT], 1, "White")
    canvas.draw_line([WIDTH - PAD_WIDTH, 0],[WIDTH - PAD_WIDTH, HEIGHT], 1, "White")
    # update ball
    ball_pos[0] += ball_vel[0]
    ball_pos[1] += ball_vel[1]
    # determines if the ball strikes a paddle and increases velocity by 10% if it does - else updates score as a miss
    if ball_pos[0] <= BALL_RADIUS + PAD_WIDTH:
        if ball_pos[1] >= paddle1_pos - HALF_PAD_HEIGHT and ball_pos[1] <= paddle1_pos + HALF_PAD_HEIGHT:
            ball_vel[0] = - ball_vel[0] * 1.1
        else:
            score2 += 1
            spawn_ball(RIGHT)
    if ball_pos[0] >= WIDTH - 1 - PAD_WIDTH - BALL_RADIUS:
        if ball_pos[1] >= paddle2_pos - HALF_PAD_HEIGHT and ball_pos[1] <= paddle2_pos + HALF_PAD_HEIGHT:
            ball_vel[0] = - ball_vel[0] * 1.1
        else:
            score1 += 1
            spawn_ball(LEFT)
    if ball_pos[1] <= BALL_RADIUS or ball_pos[1] >= HEIGHT - 1 - BALL_RADIUS:
        ball_vel[1] = - ball_vel[1]
     # draw ball
    canvas.draw_circle(ball_pos, BALL_RADIUS, 2, "Red", "White")
    # update paddle's vertical position, keep paddle on the screen
    if paddle1_pos - HALF_PAD_HEIGHT <= 0:
        paddle1_pos = HALF_PAD_HEIGHT
    elif paddle1_pos + HALF_PAD_HEIGHT >= HEIGHT - 1:
        paddle1_pos = HEIGHT - HALF_PAD_HEIGHT -1
    else:
        paddle1_pos += paddle1_vel
    if paddle2_pos - HALF_PAD_HEIGHT <= 0:
        paddle2_pos = HALF_PAD_HEIGHT
    elif paddle2_pos + HALF_PAD_HEIGHT >= HEIGHT - 1:
        paddle2_pos = HEIGHT - HALF_PAD_HEIGHT - 1
    else:
        paddle2_pos += paddle2_vel
    paddle1_pos += paddle1_vel    
    paddle2_pos += paddle2_vel
    # draw paddles
    canvas.draw_line([HALF_PAD_WIDTH,(paddle1_pos - HALF_PAD_HEIGHT)], [HALF_PAD_WIDTH, (paddle1_pos + HALF_PAD_HEIGHT)], PAD_WIDTH, "White")
    canvas.draw_line([(WIDTH - 1) - HALF_PAD_WIDTH,(paddle2_pos - HALF_PAD_HEIGHT)], [(WIDTH - 1) - HALF_PAD_WIDTH, (paddle2_pos + HALF_PAD_HEIGHT)], PAD_WIDTH, "White")
    # draw scores
    canvas.draw_text(str(score1), [60, 40], 25, "Silver")
    canvas.draw_text(str(score2), [540, 40], 25, "Silver")
        
def keydown(key):
    #moves the paddles
    global paddle1_vel, paddle2_vel
    if key == simplegui.KEY_MAP["w"]:
        paddle1_vel += -3.0
    elif key == simplegui.KEY_MAP["s"]:
        paddle1_vel += 3.0
    elif key == simplegui.KEY_MAP["up"]:
        paddle2_vel += -3.0
    elif key == simplegui.KEY_MAP["down"]:
        paddle2_vel += 3.0
    
def keyup(key):
    # stops the paddle movement
    global paddle1_vel, paddle2_vel
    if key == simplegui.KEY_MAP["w"] or key == simplegui.KEY_MAP["s"]:
        paddle1_vel = 0
    if key == simplegui.KEY_MAP["up"] or key == simplegui.KEY_MAP["down"]:
        paddle2_vel = 0
def reset_button():
    new_game() 
    
# create frame
frame = simplegui.create_frame("Pong", WIDTH, HEIGHT)
frame.set_draw_handler(draw)
frame.set_keydown_handler(keydown)
frame.set_keyup_handler(keyup)
frame.add_button("RESET", reset_button, 100)
# start frame
new_game()
frame.start()
