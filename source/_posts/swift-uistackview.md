title: UIStackView的使用
date: 2015-11-23 11:41:35
tags: Swift
---

**源代码基于 Swift 2.1+ Xcode 7.1.1编写**

iOS 9 出品快速构建UI界面

`官方原话：UIStackView 类提供了一个高效的接口用于平铺一行或一列的视图组合。Stack视图使你依靠自动布局的能力，创建用户接口使得可以动态的调整设备朝向、屏幕尺寸及任何可用范围内的变化。`

其实这里很好理解了，如果用Table来看，无非就是在横向或者竖向进行排列，所以UIStackView就存在了两种不同的类型，我们只需要对UIStackView进行约束布局，设置好它的属性，然后依次将视图添加到UIStackView中，系统会自动帮助我们将UIStackView中添加的视图添加上约束。

还需要了解的一点是被UIStackView管理的视图，第一个是与UIStackView左边界对齐，最后一个是与UIStackView右边界对齐，如果设置了`vertical.layoutMarginsRelativeArrangement = true`，UIStackView将使用相关的内容和不是边界对齐。

Demo可以查看[UIStackView](https://github.com/icepy/withoutMe/tree/master/UIStackView) Swift 2.0 Xcode 7.1.1

**如何创建UIStackView**

```Swift
let vertical:UIStackView =  UIStackView(frame: CGRectZero)
vertical.axis = .Vertical
vertical.alignment = .Center
vertical.distribution = .FillEqually
vertical.spacing = 10
```

初始化一个UIStackView，然后设置对齐方式，分布方式以及间隔。

- alignment 这里可以理解为设置X,Y轴
- distribution 这里可以理解为控制高度与宽度
- spacing 每个视图互相之间的间隔

关于更精准的布局可以访问UIStackViewDistribution和UIStackViewAlignment枚举

```Swift
public enum UIStackViewDistribution : Int {

    /* When items do not fit (overflow) or fill (underflow) the space available
     adjustments occur according to compressionResistance or hugging
     priorities of items, or when that is ambiguous, according to arrangement
     order.
     */
    case Fill

    /* Items are all the same size.
     When space allows, this will be the size of the item with the largest
     intrinsicContentSize (along the axis of the stack).
     Overflow or underflow adjustments are distributed equally among the items.
     */
    case FillEqually

    /* Overflow or underflow adjustments are distributed among the items proportional
     to their intrinsicContentSizes.
     */
    case FillProportionally

    /* Additional underflow spacing is divided equally in the spaces between the items.
     Overflow squeezing is controlled by compressionResistance priorities followed by
     arrangement order.
     */
    case EqualSpacing

    /* Equal center-to-center spacing of the items is maintained as much
     as possible while still maintaining a minimum edge-to-edge spacing within the
     allowed area.
        Additional underflow spacing is divided equally in the spacing. Overflow
     squeezing is distributed first according to compressionResistance priorities
     of items, then according to subview order while maintaining the configured
     (edge-to-edge) spacing as a minimum.
     */
    case EqualCentering
}

public enum UIStackViewAlignment : Int {

    /* Align the leading and trailing edges of vertically stacked items
     or the top and bottom edges of horizontally stacked items tightly to the container.
     */
    case Fill

    /* Align the leading edges of vertically stacked items
     or the top edges of horizontally stacked items tightly to the relevant edge
     of the container
     */
    case Leading
    public static var Top: UIStackViewAlignment { get }
    case FirstBaseline // Valid for horizontal axis only

    /* Center the items in a vertical stack horizontally
     or the items in a horizontal stack vertically
     */
    case Center

    /* Align the trailing edges of vertically stacked items
     or the bottom edges of horizontally stacked items tightly to the relevant
     edge of the container
     */
    case Trailing
    public static var Bottom: UIStackViewAlignment { get }
    case LastBaseline // Valid for horizontal axis only
}
```

`这里我没有对vertical设置约束`，然后使用`addArrangedSubview`添加一个button到UIStackView中

```Swift
let button:UIButton = UIButton(type: .Custom)
vertical.addArrangedSubview(button)
//或者使用vertical.insertArrangedSubview(<#T##view: UIView##UIView#>, atIndex: <#T##Int#>)
```

还可以通过`arrangedSubviews`属性来获取一个UIStackView容器内的所有视图。

```Swift
 vertical.arrangedSubviews
```

**从UIStackView中删除**

```Swift
vertical.removeArrangedSubview(button)
button.removeFromSuperview()
```

`removeArrangedSubview`只是确保从UIStackView中删除约束，移除出视图还需要调用`removeFromSuperview`。

## 实战练习

个人是忠实的暴雪粉丝，所以这个Demo是一道选择题，喜欢《魔兽世界》么？喜欢会打上一课星，不喜欢就会删除一课星。

且拖动两个UIStackView到storyboard中，竖向排列的UIStackView命名为verticalStackView，横向水平排列的UIStackView命名为horizontalStackView，其约束关系如下：

`在verticalStackView上按住ctrl键将箭头指向superview`，左右相对superview，上相对superview，下相对horizontalStackView。`在horizontalStackView上按住ctrl键将键头指向superview`，Height设置为110，左右相对superview，上相对于verticalStackView，下相对于superview。

设置其（verticalStackView和horizontalStackView）对应方式：

- alignment 水平居中 center
- distribution fill Equally
- Spacing 20

然后依次拖动UILabel，UIImageView，UIStackView（横向水平排列），且将第三个UIStackView命名为actionStackView，设置其alignment为center，distribution为Equal Spacing，Spacing为10，拖两个UIButton到actionStackView中。

`按住ctrl连接两个actions事件出来`

```Swift
@IBAction func linkToWow(sender: UIButton) {
    let starImage:UIImageView = UIImageView(image: UIImage(named: "Star"))
    starImage.contentMode = .ScaleAspectFit
    horizontalStackView.addArrangedSubview(starImage)
    UIView.animateWithDuration(0.25, animations: {
         [unowned self] _ in
         self.horizontalStackView.layoutIfNeeded()
     })
}

@IBAction func linkToRemoveWow(sender: UIButton) {
   let lastStar:UIView? = horizontalStackView.arrangedSubviews.last
   if let removeStart:UIView = lastStar{
       horizontalStackView.removeArrangedSubview(removeStart)
       removeStart.removeFromSuperview()
       UIView.animateWithDuration(0.25, animations: {
            [unowned self] _ in
            self.horizontalStackView.layoutIfNeeded()
       })
   }
}
```
运行看看
