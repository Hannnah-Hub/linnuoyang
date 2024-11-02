Arduino中的代码 三轴传感器x轴的加速器：
```C++
#include <Wire.h>
#include <MPU6050.h>

MPU6050 mpu;

void setup() {
  Serial.begin(115200);
  Wire.begin();
  
  // 初始化MPU6050
  mpu.initialize();
  
  // 检查MPU6050连接是否正常
  if (mpu.testConnection()) {
    Serial.println("MPU6050连接成功");
  } else {
    Serial.println("MPU6050连接失败");
  }
}

void loop() {
  // 定义变量来存储X轴加速度的值
  int16_t ax;

  // 读取传感器数据
  mpu.getMotion6(&ax, NULL, NULL, NULL, NULL, NULL);

  // 打印X轴加速度值
  Serial.println((float)ax / 16384.0); // 转换为g

  // 等待一段时间再读取下一次数据
  delay(100);
}

```
Unity中的代码，以让物体出现为例：
```C#
using UnityEngine;
using System.IO.Ports;

public class Test : MonoBehaviour
{
    SerialPort port = new SerialPort("COM10", 115200);
    public float baseSpeed = 5f;  // 基础移动速度
    private string data;  // 用来存储从串口接收到的数据
    private float lastSensorValue = 0f;  // 上一个传感器值

    void Start()
    {
        port.Open();
        port.ReadTimeout = 1;  // 设置串口读数据的超时时间
    }

    void Update()
    {
        try
        {
            data = port.ReadLine();  // 读取串口发送的数据
            float sensorValue = float.Parse(data);  // 将数据转换为浮点数

            // 计算速度差值
            float speedDifference = Mathf.Abs(sensorValue - lastSensorValue);
            float currentSpeed = baseSpeed + speedDifference;  // 增加基础速度

            // 向左移动物体
            Vector3 movement = new Vector3(-currentSpeed * Time.deltaTime, 0, 0);
            transform.position += movement;  // 更新物体的位置

            // 更新上一个传感器值
            lastSensorValue = sensorValue;
        }
        catch (System.Exception)
        {
            // 捕捉异常，避免报错
        }
    }

    private void OnApplicationQuit()
    {
        // 程序退出时关闭串口
        if (port.IsOpen)
        {
            port.Close();
        }
    }
}

```

