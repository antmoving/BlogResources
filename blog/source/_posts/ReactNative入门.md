---
title: ReactNative入门
date: 2017-06-30 13:38:51
tags:
categories: ReactNative
---
本篇博客是对ReactNative官网的入门级学习，通过大概来了解开发ReactNative的过程，以及知识点的整体把握。

<!-- more -->

## 搭建环境：

* 安装命令行工具：

```
npm install -g react-native-cli
```
	
* 创建工程：

```
react-native init AwesomeProject
```

* 运行应用：

```
react-native run-android
或
react-native run-ios
```

* 重新加载应用：

```
按两次R键或Ctrl+M进入开发者菜单 
```

## 基础知识

#### Props

* Props的使用

```
<Image source={{uri: 'https://www.baidu.com/img/bd_logo1.png'}} style={{width: 200,height: 50}}></Image>
```

* 自定义组件的Props

```
class Tag extends Component {
	render() {
    return (
      <Text>Tag is {this.props.name}</Text>
    )
  }
}

export default class start extends Component {
  render() {
    return (
      <View>
        <Tag name='Health'></Tag>
        <Tag name='Psychonogocal'></Tag>
      </View>
    );
  }
}

```

#### State

每次调用setState()方法，就会同时调用组件的render()方法

```
class Blink extends Component {
  constructor(props) {
    super(props);
    this.state = {showText: true};
    setInterval(() => {
      this.setState(previousState => {
        return {showText: !previousState.showText};
      });
    }, 1000);
  }

  render() {
    let display = this.state.showText ? this.props.text : '';
    return (
      <View>
        <Text>{display}</Text>
      </View>
    );
  }
}
```

#### 样式

```
export default class start extends Component {
  render() {
    return (
      <View>
        <Text style={{color: 'red'}}>Red</Text>
        <Text style={styles.bigBlue}>BigBlue</Text>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  bigBlue: {
    color: 'blue',
    fontWeight: 'bold',
    fontSize: 30
  }
});
```

#### 高度和宽度

固定尺寸：

```
render() {
    return (
      <View>
        <View style={{width: 200,height: 50,backgroundColor: 'green'}}></View>
      </View>
    );
}
```

Flex尺寸

```
render() {
    return (
      <View style={{flex: 1}}>
        <View style={{flex: 1,backgroundColor: 'red'}}></View>
        <View style={{flex: 2,backgroundColor: 'green'}}></View>
        <View style={{flex: 3,backgroundColor: 'yellow'}}></View>
      </View>
    );
  }
```

只有父组件的尺寸大于0，使用flex的子组件才可以填充剩余的空间。

#### 使用Flexbox来布局

* flexDirection：决定组件的布局主轴方向，默认是column

* justifyContent：决定子组件沿着主轴如何分布，可选的值为：flex-start、center、flex-end、space-around、space-between
* alignItems：决定子组件沿着交叉轴如何对齐，可选的值为：flex-start、center、flex-end、stretch

```
render() {
    return (
      <View style={{flex: 1, flexDirection: 'column',justifyContent: 'space-around',alignItems: 'center'}}>
        <View style={{width: 50,height: 50,backgroundColor: 'red'}}></View>
        <View style={{width: 50,height: 50,backgroundColor: 'green'}}></View>
        <View style={{width: 50,height: 50,backgroundColor: 'yellow'}}></View>
      </View>
    );
  }
```

#### 处理文本输入

```
constructor(props) {
    super(props);
    this.state = {text: ''}
  }
render() {
    return (
      <View style={{padding: 10}}>
        <TextInput style={{height: 40}} placeholder='Type here to translate' onChangeText={(text) => this.setState({text})}>
        </TextInput>
        <Text style={{padding: 10, fontSize: 42}}>
          {this.state.text.split(' ').map((word) => word && '*').join(' ')}
        </Text>
      </View>
    );
}
```

TextInput允许输入文本，每次输入的文本改变时，就会调用onChangeText()

#### 处理Touch

```
<View>
      <Button
        onPress={() => {Alert.alert('You tapped the button')}}
        title='Press Me'/>
</View>
```

可以使用"Touchable"组件来构建自定义按钮，使用哪种"Touchable"组件取决于想要提供哪种类型的反馈。

* TouchableHighlight
* TouchableNativeFeedback
* TouchableOpacity
* TouchWithoutFeedback

```
export default class start extends Component {
  _onPressButton() {
    Alert.alert('You tapped the button');
  }

  _onLongPressButton() {
    Alert.alert('You long-pressed the button');
  }
  render() {
    return (
      <View style={styles.constainer}>
        <TouchableHighlight onPress={this._onPressButton} underlayColor="white">
          <View style={styles.button}>
            <Text style={styles.buttonText}>TouchableHighlight</Text>
          </View>
        </TouchableHighlight>

        <TouchableOpacity onPress={this._onPressButton} >
          <View style={styles.button}>
            <Text style={styles.buttonText}>TouchableOpacity</Text>
          </View>
        </TouchableOpacity>

        <TouchableNativeFeedback onPress={this._onPressButton} background={Platform.OS === 'android' ? TouchableNativeFeedback.SelectableBackground() : ''}>
          <View style={styles.button}>
            <Text style={styles.buttonText}>TouchableNativeFeedback</Text>
          </View>
        </TouchableNativeFeedback>

        <TouchableWithoutFeedback onPress={this._onPressButton}>
          <View style={styles.button}>
            <Text style={styles.buttonText}>TouchableWithoutFeedback</Text>
          </View>
        </TouchableWithoutFeedback>

        <TouchableHighlight onPress={this._onPressButton} onLongPress={this._onLongPressButton} underlayColor='white'>
          <View style={styles.button}>
            <Text style={styles.buttonText}>Touchable with Long Press</Text>
          </View>
        </TouchableHighlight>
      </View>
    );
  }
}
```

#### ScrollView

```
render() {
    return (
      <View>
        <ScrollView>
          <Text style={{fontSize: 96}}>bananas</Text>
          <Image source={require('./img/main.png')}></Image>
          <Text style={{fontSize: 96}}>pullup</Text>
          <Image source={require('./img/main.png')}></Image>
          <Text style={{fontSize: 96}}>tweak</Text>
          <Image source={require('./img/main.png')}></Image>
        </ScrollView>
      </View>
    );
  }

```

#### List View

FlatList组件展示滚动的列表，数据源结构是相似的，该组件需要两个属性：data和renderItem。

```
<FlatList
          data={[{key: 'Devin'},{key: 'Jackson'},{key: 'James'},{key: 'Joel'}]}
          renderItem={({item}) => <Text style={styles.item}>{item.key}</Text> }>
</FlatList>
```

SectionList

```
<SectionList
          sections={[{title: 'D', data: ['Devin']},
                     {title: 'J', data: ['Jackson','James','Joel']}]}
          renderItem={({item}) => <Text style={styles.item}>{item}</Text>}
          renderSectionHeader={({section}) => <Text style={styles.sectionHeader}>{section.title}</Text>}>

</SectionList>
```

#### 网络

ReactNative提供Fetch来实现网络需求，其中fetch()方法返回Promise。fetch()方法可接受第二个参数，用来自定义HTTP请求：请求头，请求方法，请求参数。

```
fetch('https://mywebsite.com/endpoint/', {
  method: 'POST',
  headers: {
    'Accept': 'application/json',
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    firstParam: 'yourValue',
    secondParam: 'yourOtherValue',
  })
})
```

网络请求完整的例子：

```
constructor(props) {
    super(props);
    this.state = {
      isLoading: true
    }
  }
  componentDidMount() {
    return fetch('https://facebook.github.io/react-native/movies.json')
        .then((response) => response.json())
        .then((responseJson) => {
          let ds = new ListView.DataSource({rowHasChanged: (r1,r2) => r1 !== r2});
          this.setState({
            isLoading: false,
            dataSource: ds.cloneWithRows(responseJson.movies)
          })
        })
        .catch((error) => {
          console.error(error);
        })
  }
  render() {
    if (this.state.isLoading) {
      return (
        <View style={{flex: 1,paddingTop: 20}}>
          <ActivityIndicator/>
        </View>
      );
    }
    return (
      <View style={{flex: 1,paddingTop: 20}}>
        <ListView
          dataSource={this.state.dataSource}
          renderRow={(rowData) => <Text>{rowData.title}, {rowData.releaseYear}</Text>}>

        </ListView>
      </View>
    )
  }
```


