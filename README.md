# Overview - FTC Robotics
For the individual project assignment I would like to present and show a sample of the work I did, as part of a robotics highscool team called [Bionic Royals](https://bionicroyals.org/) competing in FTC.
> FIRST Tech Challenge (FTC), is a robotics competition for students in grades 7–12 to compete head to head, by designing, building, and programming a robot to compete in an alliance format against other teams. Anually a game theme is anounced with rulesets where the goal for teams is to design robots that can score as many points as possible. 

All game themes feature a automated period consisting of 30 seconds where teams need precode robot movements to score points according to the rulesets. After that teams have 2 minutes to manually control the robots through video game controllers and again score as many points as possible.

# Schematics Plan - Electronics and Connectivity
![Untitled](https://github.com/user-attachments/assets/2976fd06-5087-49e0-a390-654dd1bcce46)

In theory this is how every team's robot should look under the hood (altough nowadays teams can buy **Control Hubs** and use it as the **Android Robot Controller**)

Two Android devices operate the robot. One is mounted directly on the robot, and the other one is connected to controllers, with which drivers can operate the robot. The one placed on the robot is where the code is installed (will talk about this part later) and basically acts as a Controller connecting to the motors and servomotors through a **Expansion Hub** (you can think of it as basically a breadboard). Both Android Devices have a mobile app teams can download from the Play Store and pair them through WiFi

# Pre-requisites - Components
Teams have selected vendors where they can acquire parts:
 - Electronics (e.g. [Rev Robotics](https://www.revrobotics.com/duo/electronics/control-system/))
 - Building Parts (e.g. [GoBilda](https://www.gobilda.com/))

All vendors post CAD's of all of the components and what good teams do (and ours use to do) is save time when designing the robot by using CAD assembly-ing software. We used SolidWorks to both virtually assembly the robot and also design custom 3D parts where they were needed.

## Key components
### DC Motors
Teams are allowed to use a maximum 6 DC Motors across their robot. This rule enforces teams to take important decisions and sacrifices on where to use the motors (for example there are situations where Continous Servo Motors do the job), what type of gear ratios to use (maybe for the wheeltrain of the robot you would a more balanced gear ratio, but for an extending arm operated by multiple pullies the torque is more important). You might not always to have the budget to buy specific gear ratios motors and in this situation you can always adjust the torque & rpm through gearing. We always used 4 DC Motors for the wheeltrain of the robots (one for every wheel since we used mecanum wheels). The remaining were used for example in a flywheel that launches game elements (where high RPM was important) or in a arm that helped the robot latch onto a hook and lift itself (where high torque was needed)

<img width="918" alt="image" src="https://github.com/user-attachments/assets/587f945b-c445-4e71-894c-6a3135c2b0c9" />

### Wheels
The most popular wheels in FTC are by far Mecanum Wheels. The advantage this wheels bring are the fact that with careful programming (and some vector math) they allow you to move in all directions. This is highly useful for the game theme FTC has where speed and versatility is most important.

![image](https://github.com/user-attachments/assets/7195e8d2-47bb-4e43-b5c6-a57ac841613e)

### Linear Slides
![image](https://github.com/user-attachments/assets/4ea5e200-01cc-47f5-8a1a-40d70965c72e)

### Servo Motors
Servos feature 2 modes of operation: set positions and continous motion. For example in this [video of our robot](https://youtu.be/u_0hZlUXJu0?si=YvdB_HfnmGywFUpc&t=166)(the one on the right) we can see multiple servos at play in both modes. First of all at the top of the extending arm there's a crane that flips the game element (the lego-like cubes) from inside the robot to the outside. After that it's not really visible but the robot has 2 other servos with which it latches to the board where game elements are placed and drags it (this scores points). And at the end we also got points if we "parked" in the blue taped corner area. As you can see we found a loophole in the rulesets with which we could park in that area without actualy being in that area. We used a continous servo (since we didn't have any more motors due to the limit) that spins a wheel extending a measuring tape into that area and this basically counts us as parked.

# Coding
For the coding part we used the FTC SDK
>​The FIRST Tech Challenge Software Development Kit (FTC SDK) is a comprehensive suite of tools designed to facilitate the development and execution of software on FTC competition robots. It provides teams with the necessary resources to program their robots using various programming environments and languages.

Previously I talked about how we used a phone that acted as a controller for the robot. There are multiple ways, some simpler than others, to program the robot. One that gives you the most control but might be harder for teams with no coding experience (especially as a highschool student) is the [FTCRobotController](https://github.com/FIRST-Tech-Challenge/FtcRobotController/tree/master). This is an Android Studio project that helps us write **OpModes** that can then be installed on the phone and then selected on the FTC Application (available on the Play Store).

Here's a simple sample code where we can see how the different motors and sensors are being loaded and how does the lifecycle of the code works.
```java
@TeleOp
public class MyFIRSTJavaOpMode extends LinearOpMode {
    private Gyroscope imu;
    private DcMotor motorTest;
    private DigitalChannel digitalTouch;
    private DistanceSensor sensorColorRange;
    private Servo servoTest;

    @Override
    public void runOpMode() {
        // Here we load the motors, servos and sensors
        // The second argument are the ids of the parts that we set when we first setup the robot throught the FTC Mobile app
        imu = hardwareMap.get(Gyroscope.class, "imu");
        motorTest = hardwareMap.get(DcMotor.class, "motorTest");
        digitalTouch = hardwareMap.get(DigitalChannel.class, "digitalTouch");
        sensorColorRange = hardwareMap.get(DistanceSensor.class, "sensorColorRange");
        servoTest = hardwareMap.get(Servo.class, "servoTest");

        telemetry.addData("Status", "Initialized");
        telemetry.update();
        // Wait for the game to start (driver presses PLAY)
        waitForStart();

        // run until the end of the match (driver presses STOP)
        while (opModeIsActive()) {
            telemetry.addData("Status", "Running");
            telemetry.update();

        }
    }
}
```

Here's an example on how we used to code with some clever maths the holonomic of the Mecanum Wheels

```java
@TeleOp
public class MecanumTeleOp extends LinearOpMode {
    @Override
    public void runOpMode() throws InterruptedException {
        // Declare our motors
        DcMotor frontLeftMotor = hardwareMap.dcMotor.get("frontLeftMotor");
        DcMotor backLeftMotor = hardwareMap.dcMotor.get("backLeftMotor");
        DcMotor frontRightMotor = hardwareMap.dcMotor.get("frontRightMotor");
        DcMotor backRightMotor = hardwareMap.dcMotor.get("backRightMotor");

        // This is how we can reverse the rotation direction of motors if we have different ways we position DC Motor
        // For example here right motors are reversed
        frontRightMotor.setDirection(DcMotorSimple.Direction.REVERSE);
        backRightMotor.setDirection(DcMotorSimple.Direction.REVERSE);

        waitForStart();

        while (opModeIsActive()) {
            double y = -gamepad1.left_stick_y;
            double x = gamepad1.left_stick_x * 1.1; // Counteract imperfect strafing
            double rx = gamepad1.right_stick_x;

            // Denominator is the largest motor power (absolute value) or 1
            // This ensures all the powers maintain the same ratio,
            // but only if at least one is out of the range [-1, 1]
            double denominator = Math.max(Math.abs(y) + Math.abs(x) + Math.abs(rx), 1);
            double frontLeftPower = (y + x + rx) / denominator;
            double backLeftPower = (y - x + rx) / denominator;
            double frontRightPower = (y - x - rx) / denominator;
            double backRightPower = (y + x - rx) / denominator;

            frontLeftMotor.setPower(frontLeftPower);
            backLeftMotor.setPower(backLeftPower);
            frontRightMotor.setPower(frontRightPower);
            backRightMotor.setPower(backRightPower);
        }
    }
}
```

## FTC Mobile
![image](https://github.com/user-attachments/assets/b0c00967-783d-4974-a52d-2465b89f894b)

