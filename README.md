## 掷骰子

```用到的知识点：
1，UIImageView的动画 --旋转动画
2，高级动画--组动画
3，玩法组合--随机数产生，骰子数值回调
4，加入系统摇一摇功能
5，闭包回调，枚举类型运用
```

##  UIImageView的动画

```
//也可用晃动动画，这里采用了图片动画
UIImageView 自己封装好动画，需要传入图片数组
//转动骰子的载入
let myImages:[UIImage] = [UIImage(named:"7.png")!,UIImage(named:"8.png")!,UIImage(named:"9.png")!]
//骰子1的转动图片切换
let imageDong11 = UIImageView(frame: CGRect(x: 85.0, y: 115.0, width: 90.0,height: 90.0))
//设置图片动画切换时间
imageDong11.animationDuration = 0.1
//设置图片动画资源
imageDong11.animationImages = myImages
//开启动画
imageDong11.startAnimating()

```
##  骰子1的组动画

```
//设置动画
let spin = CABasicAnimation(keyPath: "transform.rotation")
spin.duration = animations
spin.toValue = Double.pi * 16.0
//******************位置变化***************
//设置骰子1的位置变化
//注意不要染骰子运动到屏幕之外
let dice1Point = self.getRandomNumbers(8, lenth: UInt32(ScreenWidth))
//用关键帧动画 ，设置四个点，按照这四个点移动
let p1 = CGPoint(x: CGFloat(dice1Point[0]), y: CGFloat(dice1Point[1]))
let p2 = CGPoint(x: CGFloat(dice1Point[2]), y: CGFloat(dice1Point[3]))
let p3 = CGPoint(x: CGFloat(dice1Point[4]), y: CGFloat(dice1Point[5]))
let p4 = CGPoint(x: CGFloat(dice1Point[6]), y: CGFloat(dice1Point[7]))
//把点转换成NSValue数组下一步要用
let keypoint = [NSValue(cgPoint: p1),NSValue(cgPoint: p2),NSValue(cgPoint: p3),NSValue(cgPoint: p4)]
//初始化动画的属性是位置动画
let animation1 = CAKeyframeAnimation(keyPath: "position")
animation1.values = keypoint
animation1.duration = animations
imageDong11.layer.position = CGPoint(x: ScreenWidth / 2  - 60, y: ScreenHeight / 2.0 - 50)
//骰子1的动画组合
let animGroup1 = CAAnimationGroup()
animGroup1.animations = [animation1]
animGroup1.duration = animations
//动画结束时会调用代理方法，将计算好的骰子动画传给控制器
animGroup1.delegate = self
imageDong11.layer.add(animGroup1, forKey: "position")

//随机产生不同的号码
func getRandomNumbers(_ count:Int,lenth:UInt32) -> [Int] {
    var randomNumbers = [Int]()
    for _ in 0...(count - 1) {
        var number = Int()
        number = Int(arc4random_uniform(lenth))+1
        while randomNumbers.contains(number) {
        number = Int(arc4random_uniform(lenth))+1
        }
            randomNumbers.append(number)
        }
    return randomNumbers
}

```
## 加入摇一摇动画

```
加入系统的摇一摇动画（晃动手机就调用）
这个比较简单
//导入系统库
import AudioToolbox
//调用并重写系统方法
override func motionBegan(_ motion: UIEventSubtype, with event: UIEvent?) {
        if motion == .motionShake {
            if isDiceMoving == false {
            //开始动画
            self.diceAnimationStart()
        }
    }
}
//模拟器调用方法
点击模拟器
Simulator -> Hardware -> Shake Gestrue
```


## 动画结束时的回调

```
func animationDidStop(_ anim: CAAnimation, finished flag: Bool) {
//隐藏图片
image1.isHidden = true
//停止图片的动画
imageDong1.stopAnimating()
//色子1
let dice1 = Int(arc4random_uniform(6)) + 1
//设置骰子停止时的显示的图片
imageDong1.image = UIImage(named: "\(dice1).png")
//动画结束时将骰子的数组传出到控制器
self.animationStop(true, diceArr: [])
补充，骰子的玩法类型比较多
"和值","三同号","二同号","三不同号","二不同号"
所以定义了一个  枚举enum
//色子玩法类型
enum DiceAnimationType {
    //和值
    case hzType
    //三同号
    case same3Type
    //二同号
    case same2Type
    //三不同号
    case diff3Type
    //二不同号
    case diff2Type
    }

}
```

## 方便自己
