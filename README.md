# FTC Robotics
For the individual project assignment I would like to present and show a sample of the work I did, as part of a robotics highscool team called [Bionic Royals](https://bionicroyals.org/) competing in FTC.
> FIRST Tech Challenge (FTC), is a robotics competition for students in grades 7–12 to compete head to head, by designing, building, and programming a robot to compete in an alliance format against other teams. Anually a game theme is anounced with rulesets where the goal for teams is to design robots that can score as many points as possible. 

All game themes feature a automated period consisting of 30 seconds where teams need precode robot movements to score points according to the rulesets. After that teams have 2 minutes to manually control the robots through controllers and again score as many points as possible.

# Schematics Plan
![Untitled](https://github.com/user-attachments/assets/2976fd06-5087-49e0-a390-654dd1bcce46)

In theory this is how every team's robot should look under the hood (altough nowadays teams can buy "Control Hubs" and use it as the **Android Robot Controller**)

Two Android devices operate the robot. One is mounted directly on the robot, and the other one is connected to controllers, with which drivers can operate the robot. The one placed on the robot is where the code is installed (will talk about this part later) and basically acts as a Controller connecting to the motors and servomotors through a **Expansion Hub** (you can think of it as basically a breadboard). Both Android Devices have a mobile app teams can download from the Play Store and pair them through WiFi

# Pre-requisites (Components)
Teams have selected vendors where they can acquire parts:
 - Electronics (e.g. [Rev Robotics](https://www.revrobotics.com/duo/electronics/control-system/))
 - Building Parts (e.g. [GoBilda](https://www.gobilda.com/))

All vendors post CAD's of all of the components and what good teams do (and ours use to do) is save time when designing the robot by using CAD assembly-ing software. We used SolidWorks to both virtually assembly the robot and also design custom 3D parts where they were needed.

## Key components
### DC Motors
Teams are allowed to use a maximum 6 DC Motors across their robot. This rule enforces teams to take important decisions and sacrifices on where to use the motors (there are situations where Continous Servo Motors do the job), what type of gear ratios to use (maybe for the powertrain of the robot you would a more balanced gear ratio, but for an extending arm operated by multiple pullies the torque is more important). You might not always to have the budget to buy specific gear ratios motors and in this situation you can always  . We always used 4 DC Motors for the powertrain of the robots (one for every wheel)

<img width="918" alt="image" src="https://github.com/user-attachments/assets/587f945b-c445-4e71-894c-6a3135c2b0c9" />
### Wheels
The most popular wheels in FTC are Mechanum Wheels. The advantage this wheels bring are the fact that with careful programming (and some vector math) they allow you to move in all directions. This is highly 
