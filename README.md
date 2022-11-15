# Research-Track-Assignment1
Professor: Carmine Tommaso Recchiuto,
Student: Aurora Bottino

# Installing and running 
The simulator requires a Python 2.7 installation, the pygame library, PyPyBox2D, and PyYAML.
Once the dependencies are installed, we can simply run the assignment.py script to test out the simulator.

# Robot API
The API for controlling a simulated robot is designed to be as similar as possible to the SR API.

# Goal of the assignment
The goal of the assignment is to write a python node that searches and finds a silver token in the environment and pairs it to a golden one.

# Elements of the project
ENVIRONMENT: 

![Schermata del 2022-11-10 13-05-51](https://user-images.githubusercontent.com/114871147/201389352-cddbfb43-1d49-4c64-aace-fec3763bf4f8.png)
 
ROBOT:

![robot](https://user-images.githubusercontent.com/114871147/201161427-e545f624-df63-4ebe-b1bf-54d17ba0260e.png)


SILVER TOKENS:

![token_silver](https://user-images.githubusercontent.com/114871147/201508169-6866d821-20f9-4c9c-9d73-7a16ea99413a.png)

GOLDEN TOKENS:

![token](https://user-images.githubusercontent.com/114871147/201508178-61cd5fcd-727d-46b8-9690-0733a3f06856.png)

# Motors
The simulated robot has two motors configured for skid steering, connected to a two-output Motor Board. The left motor is connected to output 0 and the right motor to output 1.

The Motor Board API is identical to that of the SR API, except that motor boards cannot be addressed by serial number. So, to turn on the spot at one quarter of full power, one might write the following:
![Schermata del 2022-11-11 12-46-59](https://user-images.githubusercontent.com/114871147/201389632-f51a4f36-704a-4e95-a398-2a20b48cf5e6.png)

The functions used to activate the motor are drive(speed,time) and turn(speed,time): the first one makes the robot go straight, for a certain time at a certain speed speed, while the second makes it turn, for a certain time and at a certain speed.
 
# The Grabber
The robot is equipped with a grabber, capable of picking up a token which is in front of the robot and within 0.4 metres of the robot's centre. To pick up a token, we call the R.grab() function. 
If the R.grab() is successful the robot will move the token, otherwise it means the robot is not close enough, so the program will act properly. To release the token we use the R.release() function.

# Vision
To help the robot find tokens and navigate, each token has markers stuck to it, as does each wall. The R.see method returns a list of all the markers the robot can see, as Marker objects. The robot can only see markers which it is facing towards.

Each Marker object has the following attributes:
![Schermata del 2022-11-11 12-55-52](https://user-images.githubusercontent.com/114871147/201390770-dd9254df-ba9c-4d68-9fe4-5f59ea0817bf.png)


For example, the following code lists all of the markers the robot can see
![Schermata del 2022-11-11 12-46-54](https://user-images.githubusercontent.com/114871147/201390492-0a9b2aba-3dac-48a5-8e5d-35b53fae42c2.png)

 
# CODE
First of all, we settle the thresholds for the control of linear distance and the control for orientation:

![Schermata del 2022-11-13 07-39-32](https://user-images.githubusercontent.com/114871147/201517619-fc078dfb-9839-4ebc-85b6-cbfbc46524e2.png)

Also, we define: the boolean variable for letting the robot know if it has to look for a silver or for a golden token, the instance of the class Robot and the two lists of silver tokens and golden tokens that we will use to control the matching. 

![Schermata del 2022-11-13 07-47-34](https://user-images.githubusercontent.com/114871147/201517625-02e23961-daa4-461e-843f-5bac425ee986.png)

Then, we have to define the functions used to activate the motors, which are, as said before, the function drive(speed, time) and turn(speed,time):

![Schermata del 2022-11-11 13-16-06](https://user-images.githubusercontent.com/114871147/201394246-cb6d1928-ace1-4dbd-b393-d2048533c6f9.png)

After, we call the functions find_silver_token() and find_golden_token(), to retrieve the distance and the angle between the robot and the closest token:

![Schermata del 2022-11-13 07-58-00](https://user-images.githubusercontent.com/114871147/201509884-b485df30-7752-4247-ada5-b372e7c00859.png)

Now, we create the Robot control loop with the fuction 'while' and we start looking for a silver token. 
If no token is detected, we make the robot turn. If the distance between the robot and the token is less than d_th meters, it means we are close to the token and the robot can grab it, by using the method grab() of the class Robot. If the robot grabs the token,we get the code corresponding to the that silver token, the list_silver_token will be updated, adding the silver token just grabbed, so that this token won't be grabbed by the robot anymore. In order to update the list, Python’s append() function has been used: Python’s append() function inserts a single element into an existing list. 
Then, we modify the value of the variable silver, so now we will look for the other type of token. Otherwise, the robot should be driven toward the token. Also, if the angle between the robot and the silver token (-1 if no token is detected) is between -a_th<= rot_y <= a_th, the robot is well aligned with the token and we can go forward. Instead, if the angle between the robot and the silver token is rot_y < -a_th or rot_y > a_th, the robot is not well aligned with the token so we move it on the left or on the right.

![Schermata del 2022-11-13 08-47-13](https://user-images.githubusercontent.com/114871147/201511625-1de98767-81ba-489d-8424-76200c92c692.png)

Then, we look for a golden token that is not already matched with a silver one. If no token is detected, we make the robot turn. If the distance between the robot and the token is less than 1.4 * d_th, it means the robot is close to the golden token not matched yet, so we release the silver token grabbed before with the method release() of the class Robot. If the silver token is released, we get the code corresponding to the that golden token, the list_golden_token will be updated, adding the golden token that is matched with the silver token, so the robot won't consider it anymore. Again, we use the function append() to update our list. 
Then, we modify the value of the variable silver, so now we will look for the other type of token. If the angle between the robot and the golden token (-1 if no token is detected) is between -a_th<= rot_y <= a_th, the robot is well aligned with the token and we can go forward.
Instead, if the angle between the robot and the golden token is rot_y < -a_th and rot_y > a_th, the robot is not well aligned with the token so we move it on the left or on the right. 

![Schermata del 2022-11-13 14-47-53](https://user-images.githubusercontent.com/114871147/201525162-10c21c59-6ac7-4bf5-a390-907a34576f94.png)


When all the tokens are paired, our work is done and we can exit the program. 

# FLOWCHART

![Schermata del 2022-11-13 11-31-23](https://user-images.githubusercontent.com/114871147/201517240-6967ec33-2314-4ffd-8bbe-8885791b11e9.png)

# Possible Improvements 
A possible improvement may be to make the robot follow a certain desired path, for example a clockwise or a counterclockwise tragectory.
