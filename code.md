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
