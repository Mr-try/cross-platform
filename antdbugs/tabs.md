- RN的样式不好改
- RN中android不能开启动画，否则虽然切换过去了，但是title并没有切换
- TabPane并不支持包裹组件
  > 方案1 是自己开发diy tabs

  > 方案2 可以忽略TabPane，只把tabs切换当作样式工具来用
    tabs的onchange事件切换数据源
- RN上tabs切换title是切换不过去，是有两个需要参数要注意
    - defaultActiveKey:可以定义this.key在onchange中动态指定该值
    - activeKey:同上，当然也可以随state、props动态传入
    - 不要忽略第二条
- tabs的key只能是字符串
