Arduino中的代码 以超声波传感器测试距离为例：
```C++
#define TRIG_PIN 9  // 触发信号引脚
#define ECHO_PIN 10 // 回声信号引脚

void setup() {
    Serial.begin(115200);  // 初始化串口通信，设置波特率为115200
    pinMode(TRIG_PIN, OUTPUT);  // 设置触发引脚为输出
    pinMode(ECHO_PIN, INPUT);   // 设置回声引脚为输入
}

void loop() {
    long duration, distance;
    
    // 发出超声波信号
    digitalWrite(TRIG_PIN, LOW);
    delayMicroseconds(2);
    digitalWrite(TRIG_PIN, HIGH);
    delayMicroseconds(10);
    digitalWrite(TRIG_PIN, LOW);

    // 读取回声引脚，计算回声持续时间
    duration = pulseIn(ECHO_PIN, HIGH);

    // 根据超声波传播时间计算距离 (单位: 厘米)
    distance = duration * 0.034 / 2;  // 声速为0.034cm/us，除以2因为往返
    
    // 将距离打印到串口监视器，发送纯数字
    Serial.println(distance);

    delay(500);  // 每500ms测量一次
}
```
Unity中的代码，以让物体出现为例：
```C#
using UnityEngine;
using System.IO.Ports;  // 用于串口通信

public class DistanceSensorControl : MonoBehaviour
{
    SerialPort port = new SerialPort("COM9", 115200);  // 设置波特率为115200
    public GameObject controlledObject;  // 场景中要控制的物体

    void Start()
    {
        try
        {
            port.Open();  // 打开串口
            port.ReadTimeout = 1000;  // 设置串口读取超时时间为1000ms
            Debug.Log("串口已成功打开");
        }
        catch (System.Exception e)
        {
            Debug.LogError("无法打开串口: " + e.Message);
        }
    }

    void Update()
    {
        if (port.IsOpen)
        {
            try
            {
                if (port.BytesToRead > 0)  // 检查是否有数据可读取
                {
                    string data = port.ReadLine();  // 从Arduino读取一行数据
                    data = data.Trim();  // 移除可能的空格或换行符

                    // 尝试将数据解析为整数
                    if (int.TryParse(data, out int distance))
                    {
                        Debug.Log("接收到的距离: " + distance + " cm");

                        // 如果距离小于20厘米，显示物体；否则隐藏物体
                        if (distance < 20)
                        {
                            controlledObject.SetActive(true);  // 显示物体
                        }
                        else
                        {
                            controlledObject.SetActive(false);  // 隐藏物体
                        }
                    }
                    else
                    {
                        Debug.LogWarning("接收到无效的数据: " + data);
                    }
                }
            }
            catch (System.TimeoutException)
            {
                // 如果串口读取超时，没有接收到数据
                Debug.LogWarning("读取串口数据超时。");
            }
            catch (System.Exception e)
            {
                // 其他异常处理
                Debug.LogError("读取串口数据时出错: " + e.Message);
            }
        }
    }

    void OnApplicationQuit()
    {
        if (port.IsOpen)
        {
            port.Close();  // 在退出应用时关闭串口
        }
    }
}
```
使用注意事项：
1.Arduino中输出到串口监视器的输出必须是纯数字（数值的输出 不要带任何文字内容）     
2.Arduino和Unity互斥，打开Arduino的同时运行Untiy游戏会显示拒绝访问。关掉arduino即可。    
3.可以以我的代码方法为例，问Chatgpt修改内容。先让Chatgpt学习方法，再把你的传感器引入，实现物体出现的功能。这个功能成功后，再去操作车车的移动速度。    
4.波特率。115200的波特率适合arduino和unity之间的通信，但是不适合arduino的串口监视器打印内容。如果想要查看串口监视器的打印内容，把波特率调到9600.若想实现通信，unity中的波特率和arduino中的波特率必须是一样的。   
