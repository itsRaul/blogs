---
title: react-native-navigation
date: 2019-06-17
tags: ['React Native']
categories: React Native
---

### react-native-navigation3.x
<!-- more -->

### 真机调试
在iOS上
打开RCTWebSocketExecutor.m文件，将"localhost"改为你电脑的ip，
然后在Developer Menu下单击"Debug JS Remotely"启动远程调试功能

```
NSString *host = [[_bridge bundleURL] host] ?: @"localhost";
```

### react生命周期
Mounting-装载
Updating-更新
Unmounting-卸载
组件初始化会调用构造方法constructor。
然后会调用render方法进行渲染，相关的组件和节点会挂载到真实的DOM节点去。render方法执行完可以获取真实的DOM节点。
render方法执行完回调componentDidMount方法,可以获取真实的DOM节点。
更新阶段，每个组件都有更新状态,setState，重新渲染之后componentDidUpdate会被回调
卸载阶段会调用componentWillUnmount

### React Navigation3.x

#### 安装

```
npm i react-navigation  react-native-gesture-handler -S 

react-native link react-native-gesture-handler
```

#### createStackNavigator
为你的应用程序提供一种在每个新屏幕放置在堆栈顶部的屏幕之间转换的方法

```
AppNavigators.js 
引入页面文件，配置标题栏
export const AppStackNavigator = createStackNavigator({
  HomePage: {
    screen: HomePage
  },

  //动态方式获取title
  Page1: {
    screen: Page1,
    navigationOptions: ({navigation}) => ({
      title: `${navigation.state.params.name}页面名字`
    })
  },

  //静态配置title
  Page2: {
    screen: Page2,
    navigationOptions: {
      title: 'This is Page2'
    }
  },

  //动态方式配置
  Page3: {
    screen: Page3,
    navigationOptions: (props) => {
      const { navigation } = props
      const { state,setParams } = navigation
      const { params } = state
      return {
        title: params.title ? params.title : 'This is Page3',
        headerRight: (
          <Button
            title={params.mode === 'edit' ? '保存' : '编辑'}
            onPress={() =>
                setParams({mode: params.mode === 'edit' ? '' : 'edit'})}
          />
        ),
      }
     
    }
  },
})
```
 
```
APP.js

需要注册createAppContainer
import {AppStackNavigator} from './navigators/AppNavigators'
import {createAppContainer} from 'react-navigation'
export default createAppContainer(AppStackNavigator);
```

3. 页面传参和接受参数

```
HomePage.js

//通过this.props.navigation.navigate进行页面跳转
export default class HomePage extends Component {
  render() {
    const {navigation} = this.props
    return (
      <Button
        title={'Go to Page1'}
        onPress={() => {
          navigation.navigate('Page1',{name:'参数'})
        }}
      />
    )
  }
}

---
Page1.js

//通过this.props.navigation.state.params接收传过来的参数
export default class Page1 extends Component {
  render() {
    const {navigation} = this.props
    return (
      <View>
        {navigation.state.params.name}
      <View>
    )
  }
}
```

#### createBottomTabNavigator+createMaterialTopTabNavigator
1. createBottomTabNavigator：创建底部导航栏组件
2. createMaterialTopTabNavigator：创建顶部导航栏组件

```
const AppBottomNavigator = createBottomTabNavigator({
  Page1: {
    screen: Page1,
    navigationOptions: {
      tabBarLabel: '最热',
      tabBarIcon: ({tintColor, focused}) => (
        <Ionicons
          name={'ios-home'}
          size={26}
          style={{color: tintColor}}
        />
      ),
    },
    
  },
  Page2: {
    screen: Page2,
    navigationOptions: {
      tabBarLabel: '趋势',
      tabBarIcon: ({tintColor, focused}) => (
        <Ionicons
          name={'ios-people'}
          size={26}
          style={{color: tintColor}}
        />
      ),
    }
  },
  Page3: {
    screen: Page3,
    navigationOptions: {
      tabBarLabel: '收藏',
      tabBarIcon: ({tintColor, focused}) => (
        <Ionicons
          name={'ios-chatboxes'}
          size={26}
          style={{color: tintColor}}
        />
      ),
    }
  },
  Page4: {
    screen: Page4,
    navigationOptions: {
      tabBarLabel: '我的',
      tabBarIcon: ({tintColor, focused}) => (
        <Ionicons
          name={'ios-home'}
          size={26}
          style={{color: tintColor}}
        />
      ),
    }
  },
},{
  tabBarOptions: {
    activeTintColor: Platform.OS === 'ios' ? '#e91e63' : '#fff',
  }
})
```

```
const AppTopNavigator = createMaterialTopTabNavigator({
  Page1: {
    screen: Page1,
    navigationOptions: {
      tabBarLabel: 'All'
    }
  },
  Page2: {
    screen: Page2,
    navigationOptions: {
      tabBarLabel: 'iOS'
    }
  },
  Page3: {
    screen: Page3,
    navigationOptions: {
      tabBarLabel: 'Page3'
    }
  },
},{
  tabBarOptions: {
    tabStyle: {
        minWidth: 50
    },
    upperCaseLabel: false,//是否使标签大写，默认为true
    scrollEnabled: true,//是否支持 选项卡滚动，默认false
    // activeTintColor: 'white',//label和icon的前景色 活跃状态下（选中）
    // inactiveTintColor: 'gray',//label和icon的前景色 活跃状态下（未选中）
    style: {
      backgroundColor: '#678',//TabBar 的背景颜色
    },
    indicatorStyle: {
      height: 2,
      backgroundColor: 'white',
    },//标签指示器的样式
    labelStyle: {
      fontSize: 13,
      marginTop: 6,
      marginBottom: 6,
    },//文字的样式
  }
})
```

```
使用把底部放到createStackNavigator

export const AppStackNavigator = createStackNavigator({
  Bottom: {
    screen: AppBottomNavigator,
    navigationOptions: {
      title: 'BottomNavigator'
    }
  },
  Top: {
    screen: AppTopNavigator,
    navigationOptions: {
      title: 'TopNavigator'
    }
  }
  HomePage: {
    screen: HomePage
  },
  //动态方式获取title
  Page1: {
    screen: Page1,
    navigationOptions: ({navigation}) => ({
      title: `${navigation.state.params.name}页面名字`
    })
  },
})
```

#### createDrawerNavigator+createSwitchNavigator

```
const DrawerNav = createDrawerNavigator({
  Page4: {
    screen: Page4,
    navigationOptions: {
      drawerLabel: 'Page4',
      drawerIcon: ({tintColor}) => (
        <MaterialIcons name="drafts" size={24} style={{color: tintColor}}/>
      ),
    }
  },
  Page5: {
    screen: Page5,
    navigationOptions: {
      drawerLabel: 'Page5',
      drawerIcon: ({tintColor}) => (
        <MaterialIcons
          name="move-to-inbox"
          size={24}
          style={{color: tintColor}}
        />
      ),
    }
  },
},
{
  initialRouteName: 'Page4', //第一次加载时初始选项卡路由的 routeName
  contentOptions: {
      activeTintColor: '#e91e63',//活动选项卡的标签和图标颜色
  },
  contentComponent: (props) => (
    <ScrollView style={{backgroundColor: '#987656', flex: 1}}>
      <SafeAreaView forceInset={{top: 'always', horizontal: 'never'}}>
        <DrawerItems {...props} />
      </SafeAreaView>
    </ScrollView>
  ) //用于呈现抽屉内容 (例如, 导航项) 的组件
}
);
```
