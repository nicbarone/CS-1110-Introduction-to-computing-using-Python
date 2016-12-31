# CS-1110-Introduction-to-computing-using-Python
Assignments for CS1110 
# breakout.py
# Nicolas Barone(njb227) Jineet Patel(jjp257)
# 12/08/2016
#used Walker White's code for determineState(self) from state.py
#used Walker White's code for updatePaddle() from arrows.py
#used code from state.py as a reference for chaning states in update(self)
"""Primary module for Breakout application

This module contains the main controller class for the Breakout application. There is no
need for any any need for additional classes in this module.  If you need more classes, 
99% of the time they belong in either the play module or the models module. If you 
are ensure about where a new class should go, 
post a question on Piazza."""
from constants import *
from game2d import *
from play import *

class Breakout(GameApp):
    """Instance is the primary controller for the Breakout App
    
    This class extends GameApp and implements the various methods necessary for processing 
    the player inputs and starting/running a game.
    
        Method start begins the application.
        
        Method update either changes the state or updates the Play object
        
        Method draw displays the Play object and any other elements on screen
    
    Because of some of the weird ways that Kivy works, you SHOULD NOT create an
    initializer __init__ for this class.  Any initialization should be done in
    the start method instead.  This is only for this class.  All other classes
    behave normally.
    
    Most of the work handling the game is actually provided in the class Play.
    Play should have a minimum of two methods: updatePaddle(input) which moves
    the paddle, and updateBall() which moves the ball and processes all of the
    game physics. This class should simply call that method in update().
    
    The primary purpose of this class is managing the game state: when is the 
    game started, paused, completed, etc. It keeps track of that in an attribute
    called _state.
    
    INSTANCE ATTRIBUTES:
         view    [Immutable instance of GView; it is inherited from GameApp]:
                the game view, used in drawing (see examples from class)
        input   [Immutable instance of GInput; it is inherited from GameApp]:
                the user input, used to control the paddle and change state
        _state  [one of STATE_INACTIVE, STATE_COUNTDOWN, STATE_PAUSED, STATE_ACTIVE]:
                the current state of the game represented a value from constants.py
        _game   [Play, or None if there is no game currently active]: 
                the controller for a single game, which manages the paddle, ball, and bricks
        _mssg   [GLabel, or None if there is no message to display]
                the currently active message
        _time   [int >= 0, 0 if state changed]:
            frames since state last changed (pressed, released) 
        _pause  [GLabel, or None if there is no message to display]
                the message when user loses a life 
        _lose   [GLabel, or None if game isn't complete]
        _3      [GLabel, or None if game isn't a countdown before the game is started or restarted if a life was lost]
        _lives	[int <= NUMBER_TURNS, 3 if no lives have been lost]
    
    STATE SPECIFIC INVARIANTS: 
        Attribute _game is only None if _state is STATE_INACTIVE.
        Attribute _mssg is only None if  _state is STATE_ACTIVE or STATE_COUNTDOWN.
        Attribute _pause is only not None if _state is STATE_PAUSED 
        
    For a complete description of how the states work, see the specification for the
    method update().
    
    You may have more attributes if you wish (you might need an attribute to store
    any text messages you display on the screen). If you add new attributes, they
    need to be documented here.
    
    LIST MORE ATTRIBUTES (AND THEIR INVARIANTS) HERE IF NECESSARY
    """

    def start(self):
        """Initializes the application.
        
        This method is distinct from the built-in initializer __init__ (which you 
        should not override or change). This method is called once the game is running. 
        You should use it to initialize any game specific attributes.
        
        This method should make sure that all of the attributes satisfy the given 
        invariants. When done, it sets the _state to STATE_INACTIVE and create a message 
        (in attribute _mssg) saying that the user should press to play a game."""

        self._state =  STATE_INACTIVE
        self._game = None
        self._mssg = GLabel( text='press any key to play', x = 200, y = 350,
                            font_name = "Zapfino.ttf")
        # self._image = None
        self._time = 0
        self._pause = None
        self._lose = None
        self._3 = None
        self._lives = NUMBER_TURNS
              
    def update(self,dt):
        #used code from state.py as a reference for chaning states
        """Animates a single frame in the game.
        
        It is the method that does most of the work. It is NOT in charge of playing the
        game.  That is the purpose of the class Play.  The primary purpose of this
        game is to determine the current state, and -- if the game is active -- pass
        the input to the Play object _game to play the game.
        
        As part of the assignment, you are allowed to add your own states.  However, at
        a minimum you must support the following states: STATE_INACTIVE, STATE_NEWGAME,
        STATE_COUNTDOWN, STATE_PAUSED, and STATE_ACTIVE.  Each one of these does its own
        thing, and so should have its own helper.  We describe these below.
        
        STATE_INACTIVE: This is the state when the application first opens.  It is a 
        paused state, waiting for the player to start the game.  It displays a simple
        message on the screen.
        
        STATE_NEWGAME: This is the state creates a new game and shows it on the screen.  
        This state only lasts one animation frame before switching to STATE_COUNTDOWN.
        
        STATE_COUNTDOWN: This is a 3 second countdown that lasts until the ball is 
        served.  The player can move the paddle during the countdown, but there is no
        ball on the screen.  Paddle movement is handled by the Play object.  Hence the
        Play class should have a method called updatePaddle()
        
        STATE_ACTIVE: This is a session of normal gameplay.  The player can move the
        paddle and the ball moves on its own about the board.  Both of these
        should be handled by methods inside of class Play (NOT in this class).  Hence
        the Play class should have methods named updatePaddle() and updateBall().
        
        STATE_PAUSED: Like STATE_INACTIVE, this is a paused state. However, the game is
        still visible on the screen.
        
        The rules for determining the current state are as follows.
        
        STATE_INACTIVE: This is the state at the beginning, and is the state so long
        as the player never presses a key.  In addition, the application switches to 
        this state if the previous state was STATE_ACTIVE and the game is over 
        (e.g. all balls are lost or no more bricks are on the screen).
        
        STATE_NEWGAME: The application switches to this state if the state was 
        STATE_INACTIVE in the previous frame, and the player pressed a key.
        
        STATE_COUNTDOWN: The application switches to this state if the state was
        STATE_NEWGAME in the previous frame (so that state only lasts one frame).
        
        STATE_ACTIVE: The application switches to this state after it has spent 3
        seconds in the state STATE_COUNTDOWN.
        
        STATE_PAUSED: The application switches to this state if the state was 
        STATE_ACTIVE in the previous frame, the ball was lost, and there are still
        some tries remaining.
        
        You are allowed to add more states if you wish. Should you do so, you should 
        describe them here.
        
        Parameter dt: The time in seconds since last update
        Precondition: dt is a number (int or float)
        """
       
        x = NUMBER_TURNS
        self._determineState()
        if self._state == STATE_NEWGAME:
            self._mssg = None
            self._game = Play()
            self._game.updatePaddle(self.input)
        elif self._state == STATE_COUNTDOWN:
            self._Countdown()
        elif self._state == STATE_ACTIVE:
            # self._image = GImage(x = 230, y = 20, width = 40, height = 20, source='beach-ball.png')
            self._3 = None
            self._game.updatePaddle(self.input)
            self._game.updateBall()
            self._game.MoveBall()
            self.messages()
        elif self._state == STATE_COMPLETE:
            self._3 = None 
            if len(self._game.getBricks()) == 0:
                self._lose = GLabel(
                    text='Congrats, 70,000$ later, and you won a game of breakout.'
                , x = 200, y = 350, font_name = "ArialBold.ttf", font_size = 15)
            else:
                self._lose =GLabel(
                    text='You lost, Congradulations!',
                    x = 250, y = 350, font_name = "ArialBold.ttf", font_size = 20)
        
    def draw(self):
        """Draws the game objects to the view.
        
        Every single thing you want to draw in this game is a GObject.  To draw a GObject 
        g, simply use the method g.draw(self.view).  It is that easy!
        
        Many of the GObjects (such as the paddle, ball, and bricks) are attributes in Play. 
        In order to draw them, you either need to add getters for these attributes or you 
        need to add a draw method to class Play.  We suggest the latter.  See the example 
        subcontroller.py from class."""


        if self._mssg is not None:
            self._mssg.draw(self.view)

        elif self._pause is not None:
            self._pause.draw(self.view)

        elif self._game is not None:
            self._game.draw(self.view)

        if self._lose is not None:
            self._lose.draw(self.view)

        if self._3 is not None:
            self._3.draw(self.view)
        
        # if self._image is not None:
        #     self._image.draw(self.view)
    
    def _determineState(self):
        #used code from State.py file given by Walker White
        """Determines the current state and assigns it to self.state
        
        This method checks for a key press, and if there is one, changes the state 
        to the next value.  A key press is when a key is pressed for the FIRST TIME.
        We do not want the state to continue to change as we hold down the key.  The
        user must release the key and press it again to change the state."""


        curr_keys = self.input.key_count

        change = curr_keys > 0
        
        if change and self._state == STATE_INACTIVE:
            self._state = STATE_NEWGAME

        elif self._state == STATE_NEWGAME:
            self._state = STATE_COUNTDOWN
        elif change and self._state == STATE_PAUSED:
            self._state = STATE_COUNTDOWN
            
    def _Countdown(self):

        """Establishes a countdown for the game.
        
        It incrememts the timer and prints it. When the timer, and thus
        the # of frames,hits 180, the game ball is created and the state
        is changed to STATE_ACTIVE. If a player loses a life, the timer starts
        again at the previous count. When it hits 360, the game ball is created
        and the state is changed to STATE_ACTIVE. The same is done if the timer hits
        540. If the timer goes above 540, the state is changed to STATE_COMPLETE
        and the game has concluded.
        
        Extension within this allows for the countdown to appear in the game
        for 3 seconds, 2 seconds, and 1 seconds. """
        #(Extension here allows for countdown to display 3,2,1 everythime
        #the frame the game starts or the user loses a life and is ready
        #to play again.)
        x = NUMBER_TURNS
        self._time = self._time + 1
        self._pause = None
        self._game.updatePaddle(self.input)
        if self._time % 180 == 1:
            self._3 = GLabel( text='3', x = 200, y = 350,
                             font_name = "ArialBold.ttf", font_size = 50)
        elif self._time % 180 == 60:
            self._3 = GLabel( text='2', x = 200, y = 350,
                             font_name = "ArialBold.ttf", font_size = 50)
        elif self._time % 180 == 120:
            self._3 = GLabel( text='1', x = 200, y = 350,
                             font_name = "ArialBold.ttf", font_size = 50)
        elif self._time == 180:
            self._3 = None
            self._game.makeBall()
            self._state = STATE_ACTIVE
        elif self._time == 360:
            x = x - 1 
            self._game.makeBall()
            self._state = STATE_ACTIVE
        elif self._time == 540:
            x = x - 2
            self._game.makeBall()
            self._state = STATE_ACTIVE
        elif self._time > 540:
            self._state = STATE_COMPLETE
        
    def messages(self):
        """calls upon helper method that checks checks if the brick count gets
        into a certain range to display a message to the user. Also changed
        state depending on if specific method is evaluated to True or False.
        checks number of lives and if it reaches a certain point will display
        message. Checks brick count that if it is equal to zero changes state
        to STATE_COMPLETE to end the game."""
        # extension
        print len(self._game.getBricks())
        self.textDisplayed()
        if len(self._game.getBricks()) == 0:
                self._state = STATE_COMPLETE 
        if self._game.gameFail() == True:
                self._state = STATE_PAUSED
                print self._state
                print self._time 
        if self._state == STATE_PAUSED:
            print 'aaaaaaaaaaaa'
            self._lives = self._lives - 1
            if self._time >= 540:
                
                self._state = STATE_COMPLETE
            else:
                self._3 = None
                if self._time == 180:
                    self._3 = GLabel( text='Might wanto ask for help..',
                    x = 200,y = 350, font_name = "ArialBold.ttf", font_size = 20)
                    self._game.
                if self._time == 360:
                    self._3 = GLabel( text='You call yourself a consultant?',
                    x = 200,y = 350, font_name = "ArialBold.ttf", font_size = 20)
                
    def textDisplayed(self):
        """method checks if the brick length is between a certain range and if it is
        sets atrribute _3 to a message that temporarily displays during that range.
        If the brick length is in between a certain range will also call on sound
        method found in Play.py file and play the sound."""
        #extension that displays messages and plays sound 
        if 86 < len(self._game.getBricks()) < 95:
                self._3 = GLabel( text='Hey good for you', x = 200, y = 350,
                                 font_name = "ArialBold.ttf",font_size = 20)
        elif 80 < len(self._game.getBricks()) < 85:
                self._3 = GLabel( text='alright I see you',
                    x = 200, y = 350, font_name = "ArialBold.ttf",font_size = 20)
        elif 60 <len(self._game.getBricks()) < 65:
                self._3 = GLabel( text='Pretty lucky', x = 200, y = 350,
                                 font_name = "ArialBold.ttf", font_size = 20)
        elif 30 <len(self._game.getBricks()) < 35:
                self._3 = GLabel( text='Thats it! You will not Win!', x = 200,
                                 y = 350, font_name = "ArialBold.ttf",
                                 font_size = 20)
                self._game.SoundUp()
        elif 25 <len(self._game.getBricks()) < 30:
                self._3 = GLabel( text='Annoying Huh? I\'ll stop', x = 200, y = 350,
                                 font_name = "ArialBold.ttf", font_size = 20)
                
        elif 20 < len(self._game.getBricks()) < 25:
                self._3 = GLabel( text='JK..', x = 200, y = 350,
                                 font_name = "ArialBold.ttf", font_size = 20)
                self._game.SoundUp()
# play.py
# Nicolas Barone(njb227) Jineet Patel(jjp257)
# 12/08/2016
# used Walker White's code from arrows.py as a reference 
"""Subcontroller module for Breakout

This module contains the subcontroller to manage a single game in the Breakout App. 
Instances of Play represent a single game.  If you want to restart a new game, you are 
expected to make a new instance of Play.

The subcontroller Play manages the paddle, ball, and bricks.  These are model objects.  
Their classes are defined in models.py.

Most of your work on this assignment will be in either this module or models.py.
Whether a helper method belongs in this module or models.py is often a complicated
issue.  If you do not know, ask on Piazza and we will answer."""
from constants import *
from game2d import *
from models import *


class Play(object):
    """An instance controls a single game of breakout.
    
    This subcontroller has a reference to the ball, paddle, and bricks. It animates the 
    ball, removing any bricks as necessary.  When the game is won, it stops animating.  
    You should create a NEW instance of Play (in Breakout) if you want to make a new game.
    
    If you want to pause the game, tell this controller to draw, but do not update.  See 
    subcontrollers.py from Lecture 25 for an example.
    
    INSTANCE ATTRIBUTES:
        _paddle [Paddle]: the paddle to play with 
        _bricks [list of Brick]: the list of bricks still remaining 
        _ball   [Ball, or None if waiting for a serve]:  the ball to animate
        _tries  [int >= 0]: the number of tries left 
    
    As you can see, all of these attributes are hidden.  You may find that you want to
    access an attribute in class Breakout. It is okay if you do, but you MAY NOT ACCESS 
    THE ATTRIBUTES DIRECTLY. You must use a getter and/or setter for any attribute that 
    you need to access in Breakout.  Only add the getters and setters that you need for 
    Breakout.
    
    You may change any of the attributes above as you see fit. For example, you may want
    to add new objects on the screen (e.g power-ups).  If you make changes, please list
    the changes with the invariants.
                  
    LIST MORE ATTRIBUTES (AND THEIR INVARIANTS) HERE IF NECESSARY
    """   
    def getBricks(self):
        """Returns: the list of bricks initiaized in Play. """
        return self._bricks
    def __init__(self):
        """**Constructor**: intitilaizes play by assigning values to specific attributes.
        Method makes sure all the invariants are satisfied for the attributes. """
        i = 0
        acc = []
        for i in range(0,10):
            for j in range(0,10):
                new = Brick(i,j)
                acc.append(new)
        self._bricks = acc
        paddle = Paddle()
        self._paddle = paddle   
        self._ball = None
        self._tries = NUMBER_TURNS
        self._image1 = Beach(x= 230)
        self._image2 = Beach(x = 100)
        self._image3 = Beach(x = 350)
        self._text = GLabel( text='poof!', x = 200, y = 350,
                            font_name = "Zapfino.ttf")
        # self._image = None)
      
    def draw(self, view):
        """Draws the game objects to the application screen.
        Draws the bricks, paddle, and ball as necessary.
        
        Parameter: The view window
        Precondition: view is a GView."""
        for i in self._bricks:
            i.draw(view)
        self._paddle.draw(view)
        self._image1.draw(view)
        self._image2.draw(view)
        self._image3.draw(view)
        if self._ball is not None:
            self._ball.draw(view)
        
    def updatePaddle(self,input):
        """Animates the paddle.
        
        Parameter input: the input of the user
        Precondition: input is a GObject."""
        # used Walker White's code from arrows.py as a reference 
        x = 6
        da = 0
        dy = 0
        if input.is_key_down('left'):
            da -= x
        if input.is_key_down('right'):
            da += x
        posx = self._paddle.getX() + da
        self._paddle.setX(posx) 
        self._paddle.setX(max(self._paddle.getX(), PADDLE_WIDTH/2))
        self._paddle.setX(min(self._paddle.getX(), GAME_WIDTH-PADDLE_WIDTH/2))
        #EXTENISON allows user to move the paddle up and down up until GAME_HEIGHT/4
        # and bellow until PADDLE_OFFSET+PADDLE_HEIGHT/2
        if input.is_key_down('down'):
            dy -= x
        if input.is_key_down('up'):
            dy += x
        posy = self._paddle.getY() + dy
        self._paddle.setY(posy)
        self._paddle.setY(min(self._paddle.getY(), GAME_HEIGHT/4))
        self._paddle.setY(max(self._paddle.getY(),
        PADDLE_OFFSET + PADDLE_HEIGHT/2))
    
    def makeBall(self):
        """makes a ball only when it is called upon in breakout."""
        self._ball = Ball()
    
    def updateBall(self):
        """ updates ball's position to make it move across the screen.
            Checks if ball and paddle collides with either paddle or brick.
            If it collides with bricks removes brick from screen and negates
            the y velocity of the ball. If it collides with the paddle it
            negates the y velocity of the ball and if it hits edges of the
            paddle it also reverse x velocity"""
            #Extension here check
        self._ball.moveBall()
        if self._paddle.collides(self._ball):
            self._ball.setColor()
            self._ball.setVy(-self._ball.getVy())
            if ((self._ball.getX() + BALL_DIAMETER/2) >=
                (self._paddle.getX() + PADDLE_WIDTH/4)):
                self._ball.setVx(-self._ball.getVx())
            elif ((self._ball.getX() - BALL_DIAMETER/2) <=
                (self._paddle.getX() - PADDLE_WIDTH/4)):
                self._ball.setVx(-self._ball.getVx())
        if self._image1.collides(self._ball):
            self._ball.setVy(-self._ball.getVy())
        if self._image2.collides(self._ball):
            self._ball.setVy(-self._ball.getVy())
        if self._image3.collides(self._ball):
            self._ball.setVy(-self._ball.getVy())
        x = -1
        for i in range(len(self._bricks)):
            if self._bricks[i].collides(self._ball):
              
                self._ball.setVy(-self._ball.getVy())
                self._ball.setColor()
                self._ball.setVx(random.uniform(1.0,5.0))
                x = i 
                self._bricks.remove(self._bricks[x])
                return
                
    def MoveBall(self):
        """calls upon method for the class ball in Models.py to change the
        x and y position by adding the x and y velocities."""
        self._ball.BounceBall()
        
    def SoundUp(self):
        """Load an audio file by creating a sound object then calling
        it for use in Breakout.py"""
        bounceSound = Sound('bounce.wav')
        bounceSound.play()
    
    def gameFail(self):
        """Returns: True or False depending on whether
        the ball passes the bottom of the screen. If ball is below the screen
        calls upon ball method found in Models.py."""
        #entension removes ball from view
        if (self._ball.getY() - BALL_DIAMETER/2) <= 0:
            self._ball.removeBall()
            return True
        return False
# models.py
# Nicolas Barone(njb227) Jineet Patel(jjp257)
# 12/08/2016
"""Models module for Breakout

This module contains the model classes for the Breakout game. That is anything that you
interact with on the screen is model: the paddle, the ball, and any of the bricks.

Technically, just because something is a model does not mean there has to be a special 
class for it.  Unless you need something special, both paddle and individual bricks could
just be instances of GRectangle.  However, we do need something special: collision 
detection.  That is why we have custom classes.
You are free to add new models to this module.  You may wish to do this when you add
new features to your game.  If you are unsure about whether to make a new class or 
not, please ask on Piazza."""   
import random 
from constants import *
from game2d import *


class Paddle(GRectangle):
    """An instance is the game paddle.
    
    This class contains a method to detect collision with the ball, as well as move it
    left and right.  You may wish to add more features to this class.
    
    The attributes of this class are those inherited from GRectangle.
    
    LIST MORE ATTRIBUTES (AND THEIR INVARIANTS) HERE IF NECESSARY
    """
    def getX(self):
        "Returns: x position of paddle"
        return self.x
    
    def getY(self):
        "Returns y position of paddle"
        return self.y
    
    def setX(self, value):
        "sets x position of paddle"
        self.x = value
    
    def setY(self, value):
        "sets y position of paddle"
        self.y = value 
    
    def __init__(self):
        """**Constructor**: Creates a new solid paddle.
        overides the GRectangle __init__ method to create a paddle.
        
        Parameter i: int used to multiply and loop through x positions of each brick.
        Precondition: i is an int > 0
        
        Parameter j: int used to multiply and loop through y positions of each brick.
        Precondition: j is an int > 0"""
        GRectangle.__init__(self,x = GAME_WIDTH/2, y = PADDLE_OFFSET,
        width = PADDLE_WIDTH, fillcolor = colormodel.BLACK, height = PADDLE_HEIGHT)
    
    def collides(self,ball):
        """Returns: True if the ball collides with this paddle by calling
        contains method in game2d.py to check if if the paddle contains
        the balls (x,y) position.
        
        Parameter ball: The ball to check.
        Precondition: ball is of class Ball."""
        
        if (self.contains(ball.x - BALL_DIAMETER/2.0, ball.y - BALL_DIAMETER/2.0)
            and ball._vx < 0):
            return True
        
        if (self.contains(ball.x + BALL_DIAMETER/2.0, ball.y - BALL_DIAMETER/2.0)
            and ball._vy < 0):
            return True

class Brick(GRectangle):
    """An instance is the game paddle.
    
    This class contains a method to detect collision with the ball.  You may wish to 
    add more features to this class.
    
    The attributes of this class are those inherited from GRectangle.
    
    LIST MORE ATTRIBUTES (AND THEIR INVARIANTS) HERE IF NECESSARY
    """
    def __init__(self, i, j):
        """**Constructor**: Creates a new solid brick.
        overides the GRectangle __init__ method to create a brick.
        
        Parameter i: int used to multiply and loop through x positions of each brick.
        Precondition: i is an int > 0
        
        Parameter j: int used to multiply and loop through y positions of each brick.
        Precondition: j is an int > 0"""
        GRectangle.__init__(self, left = BRICK_SEP_H/2 + i*(BRICK_WIDTH + BRICK_SEP_H),
        y = (GAME_HEIGHT - BRICK_Y_OFFSET)  - j* (BRICK_HEIGHT + BRICK_SEP_V),
        fillcolor = BRICK_COLOR[j%10], width = BRICK_WIDTH, height = BRICK_HEIGHT,
        linecolor = BRICK_COLOR[j%10])
    
    def collides(self,ball):
        """Returns: True if the ball collides with this brick by calling
        contains method in game2d.py to check if if the paddle contains
        the balls (x,y) position.
        
        Parameter ball: The ball to check.
        Precondition: ball is of class Ball."""
        if self.contains(ball.x - BALL_DIAMETER/2.0, ball.y + BALL_DIAMETER/2.0):
            return True
        if self.contains(ball.x - BALL_DIAMETER/2.0, ball.y - BALL_DIAMETER/2.0):
            return True
        if self.contains(ball.x + BALL_DIAMETER/2.0, ball.y + BALL_DIAMETER/2.0):
            return True
        if self.contains(ball.x + BALL_DIAMETER/2.0, ball.y - BALL_DIAMETER/2.0):
            return True
            

class Ball(GEllipse):
    """Instance is a game ball.
    
    We extend GEllipse because a ball must have additional attributes for velocity.
    This class adds this attributes and manages them.
    
    INSTANCE ATTRIBUTES:
        _vx [int or float]: Velocity in x direction 
        _vy [int or float]: Velocity in y direction 
    
    The class Play will need to look at these attributes, so you will need
    getters for them.  However, it is possible to write this assignment with no
    setters for the velocities.
    
    How? The only time the ball can change velocities is if it hits an obstacle
    (paddle or brick) or if it hits a wall.  Why not just write methods for these
    instead of using setters?  This cuts down on the amount of code in Gameplay.
    
    NOTE: The ball does not have to be a GEllipse. It could be an instance
    of GImage (why?). This change is allowed, but you must modify the class
    header up above.
    
    LIST MORE ATTRIBUTES (AND THEIR INVARIANTS) HERE IF NECESSARY"""
    
    def getVx(self):
        "Returns: the x velocity of the ball"
        return self._vx
    
    def getVy(self):
        "Returns: the y velocity of the ball"
        return self._vy
    
    def setVx(self, value):
        "sets the x velocity of the ball"
        self._vx = value
    
    def setVy(self, value):
        "sets the y velocity of the ball"
        self._vy = value
    
    def getY(self):
        "Returns: the y position of the ball"
        return self.y
    
    def getX(self):
        "Returns: the x position of the ball"
        return self.x

    def __init__(self, vx=0, vy=0):
        """**Constructor**: Creates a new solid ball
        overides the GEllipse __init__ method to create a ball.
        
        Parameter vx: int used set the x veclocity of the ball.
        Precondition: vx is an int > 0
        
        Parameter vy: used set the x veclocity of the ball.
        Precondition: vy is an int > 0"""
        # self._vx = 0.0
        self._vx = random.uniform(1.0,5.0)
        self._vx = self._vx * random.choice([-1, 1])
        self._vy = -5.0
        GEllipse.__init__(self, x = GAME_WIDTH/2, y = GAME_HEIGHT/2,
        fillcolor = colormodel.BLUE, width = BALL_DIAMETER, height = BALL_DIAMETER)
        
    def moveBall(self):
        """Changes the x and y position by adding the x and y velocities."""
        self.x = self.x + self._vx
        self.y = self.y + self._vy
        
    def BounceBall(self):
        """Checks that if the ball is at the boundary of the game window
        it negates its direction to bounce back. If it is hitting the top
        or bottom of the game window it reverses the y velocity of the ball.
        If it hits the side of the game window it reverses the x velocity of
        the ball. """
        if self.y+BALL_DIAMETER/2 >= GAME_HEIGHT:
            self._vy = -self._vy
        if self.y - BALL_DIAMETER/2 <= 0:
            self._vy = -self._vy
        if self.x + BALL_DIAMETER/2 >= GAME_WIDTH:
            self._vx= -self._vx
        if self.x - BALL_DIAMETER/2 <= 0:
            self._vx = -self._vx
            
    def removeBall(self):
        """when called moves ball position to negative values
        to take the ball out of the user's view"""
        self.y = -10
        self.x = -10
    def setColor(self):
       
        x = random.uniform(1.0, 4.0)
        if 1.0 <= x < 2.0:
            if self.fillcolor == colormodel.RED:
                self.filcolor == colormodel.BLACK
            self.fillcolor = colormodel.RED
        elif 2.0 <= x < 3.0:
            if self.fillcolor == colormodel.BLACK:
                self.fillcolor = colormodel.BLUE
            self.fillcolor = colormodel.BLACK
        elif 3.0 <= x <= 4.0:
            if self.fillcolor == colormodel.BLUE:
                self.fillcolor = colormodel.RED
            self.fillcolor = colormodel.BLUE
        # elif 4.0 <= x < 5.0:
        #     if self.fillcolor == colormodel.YELLOW:
        #         self.fillcolor = colormodel.BLUE
        #     self.fillcolor == colormodel.YELLOW
        # elif x == 5.0:
        #     self.fillcolor == colormodel.GREEN
        #     
            
        #  if self.fillcolor == colormodel.RED:
        #     self.filcolor == colormodel.BLACK
        # if self.fillcolor == colormodel.BLACK:
        #     self.fillcolor = colormodel.BLUE
        # if self.fillcolor == colormodel.BLUE:
        #     self.fillcolor = colormodel.RED   
class Beach(GImage):
    """**Constructor**: Creates a new rectangle image
        
            :param keywords: dictionary of keyword arguments 
            **Precondition**: See below.
        
        To use the constructor for this class, you should provide it with a list of 
        keyword arguments that initialize various attributes. For example, to load the 
        image `beach-ball.png`, use the constructor
        
            GImage(x=0,y=0,width=10,height=10,source='beach-ball.png')
        
        This class supports the all same keywords as `GRectangle`; the only new keyword
        is `source`.  See the documentation of `GRectangle` and `GObject` for the other
        supported keywords."""
    def __init__(self,x):
        GImage.__init__(self,x = x, y = 20, width = 70, height = 20, source='beach-ball.png')
        
    def collides(self, ball):
        """Returns: True if the ball collides with this brick by calling
        contains method in game2d.py to check if if the paddle contains
        the balls (x,y) position.
        
        Parameter ball: The ball to check.
        Precondition: ball is of class Ball."""
        if self.contains(ball.x - BALL_DIAMETER/2.0, ball.y + BALL_DIAMETER/2.0):
            return True
        if self.contains(ball.x - BALL_DIAMETER/2.0, ball.y - BALL_DIAMETER/2.0):
            return True
        if self.contains(ball.x + BALL_DIAMETER/2.0, ball.y + BALL_DIAMETER/2.0):
            return True
        if self.contains(ball.x + BALL_DIAMETER/2.0, ball.y - BALL_DIAMETER/2.0):
            return True
    def removeBeach(self):
        """removes the beach from the screen after a certain period of time"""
        self.x = -10
        self.y = -10
class Text(GLabel):
    
    
    def __init__(self,x,y, font ):
        """Instances represent an (uneditable) text label
    
    This object is exactly like a GRectangle, except that it has the possibility of
    containing some text.
    
    The attribute `text` defines the text content of this label.  Uses of the escape 
    character '\\n' will result in a label that spans multiple lines.  As with any
    `GRectangle`, the background color of this rectangle is `fillcolor`, while 
    `linecolor` is the color of the text.
    
    The text itself is aligned within this rectangle according to the attributes `halign` 
    and `valign`.  See the documentation of these attributes for how alignment works.  
    There are also attributes to change the point size, font style, and font name of the 
    text. The `width` and `height` of this label will grow to ensure that the text will 
    fit in the rectangle, no matter the font or point size.
    
    To change the font, you need a .ttf (TrueType Font) file in the Fonts folder; refer 
    to the font by filename, including the .ttf. If you give no name, it will use the 
    default Kivy font.  The `bold` attribute only works for the default Kivy font; for 
    other fonts you will need the .ttf file for the bold version of that font.  See the
    provided `ComicSans.ttf` and `ComicSansBold.ttf` for an example."""
        GLabel.__init__(text = text, x = x, y = y, font = font)
    
  
