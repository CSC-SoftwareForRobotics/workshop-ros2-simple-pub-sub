
# SIMPLE ROS2 PUBLISHER / SUBSCRIBER EXAMPLE

(loosely based on [ros2 pub/sub tutorial](https://index.ros.org/doc/ros2/Tutorials/Writing-A-Simple-Py-Publisher-And-Subscriber/))

## assumes:  
* docker
* command line skills


### Create the `docker` image for this workshop using the `Dockerfile`  

```
docker build -t ros2-simple-pub-sub .
```

check that the docker image was created successfully:

```
docker image list
```

You should see `ros2-simple-pub-sub` listed.

### Run the docker image as a container

```
docker run -it ros2-simple-pub-sub
```

Test by running command `ros2` and you should see text that includes *ros2 is an extensible command-line tool for ROS 2.*


## Download publisher / subscriber code

### Create and test Publisher

```
git clone TODO
```

```
import rclpy      # ROS CLIENT LIBRARIES
from rclpy.node import Node

from std_msgs.msg import String

class MinimalPublisher(Node):

    def __init__(self):
        super().__init__('simple_publisher')
        self.publisher_ = self.create_publisher(String, 'my_topic_name', 10)
        timer_period = 0.5  # seconds
        self.timer = self.create_timer(timer_period, self.timer_callback)
        self.i = 0

    def timer_callback(self):
        msg = String()
        msg.data = 'Hello World: %d' % self.i
        self.publisher_.publish(msg)
        self.get_logger().info('Publishing: "%s"' % msg.data)
        self.i += 1


def main(args=None):
    rclpy.init(args=args)

    minimal_publisher = MinimalPublisher()

    rclpy.spin(minimal_publisher)

    # Destroy the node explicitly
    # (optional - otherwise it will be done automatically
    # when the garbage collector destroys the node object)
    minimal_publisher.destroy_node()
    rclpy.shutdown()


if __name__ == '__main__':
    main()
```


```
import rclpy
from rclpy.node import Node

from std_msgs.msg import String


class MinimalSubscriber(Node):

    def __init__(self):
        super().__init__('minimal_subscriber')
        self.subscription = self.create_subscription(
            String,
            'topic',
            self.listener_callback,
            10)
        self.subscription  # prevent unused variable warning

    def listener_callback(self, msg):
        self.get_logger().info('I heard: "%s"' % msg.data)


def main(args=None):
    rclpy.init(args=args)

    minimal_subscriber = MinimalSubscriber()

    rclpy.spin(minimal_subscriber)

    # Destroy the node explicitly
    # (optional - otherwise it will be done automatically
    # when the garbage collector destroys the node object)
    minimal_subscriber.destroy_node()
    rclpy.shutdown()


if __name__ == '__main__':
    main()
```


`cd` into the cloned directory

edit the publisher file

```
nano simple-publisher.py
```

**TODO**:  Add scaffolding

Run the publisher script:

```
python simple-publisher.py
```

you should see command-line output like:

```
[INFO] [simple_publisher]: Publishing: "Hello World: 0"
```


### Create and test Subscriber

Open a second terminal/command-line window on your host OS.  Then list the running docker containers:

```
docker ps
```

You should see a ~12 digit hex code under 'CONTAINER ID'

```
CONTAINER ID        IMAGE                 COMMAND                  
YOUR_HEX_CODE        ros2-simple-pub-sub   "/ros_entrypoint.sh 
```

Attach to your already-running docker container by copying `YOUR_HEX_CODE` into this command and running it:

```
docker exec -it YOUR_HEX_CODE bash
``` 

Test that ros2 is available by typing the command

```
ros2
```

You should again see a long message that contains *‌ros2 is an extensible command-line tool for ROS 2.*

Remind yourself where you are in the docker container file hierarchy:

```
pwd
```

You should see `/`, meaning you're at the root level.


Edit a new file to run a subscriber:

```
nano simple-subscriber.py
```

Fill in the following:

* topic name
* what to say in response

Run the subscriber node:

```
python simple-subscriber.py
```

You should see something like:

```
[INFO] [minimal_subscriber]: I heard: "Hello World: 0"
```

indicating the receipt of each message:

Stop the subscriber node with `CTRL-C`


Activities

[GOOGLE FORM **todo:**]()


* increase the publishing rate to 50 Hz.     (What is the rate delay in seconds to achieve 50 Hz)

With your publisher running, use this command to monitor the publishing rate

```
ros2 topic hz my_topic_name
```

Paste the first 10 lines of the results here:


