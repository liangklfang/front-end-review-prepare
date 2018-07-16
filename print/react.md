#### 1.必须调用super(props)实例化this.props
```js
 class Hello extends React.Component {
      constructor(props){
        super();
        console.log(this.props);
        console.log(props);
      }
  }
    ReactDOM.render(
    <Hello name="peter"/>,
    document.getElementById('example')
    );
```
打印结果为:undefined,{name:'peter'}!

#### 2.this.state.loc===nextState.loc
```js
class Example extends React.Component{
    state = {
      loc:{
      name:"湖南",
      age:25
    }
  }
    updateLoc=()=>{
      const {loc} = this.state;
        loc.name="安徽";
        this.setState({
        loc
        });
    }
    shouldComponentUpdate(nextProps,nextState){
     console.log('this.state.loc===nextState.loc',this.state.loc===nextState.loc);
     return true
    }
    render(){
       console.log('重新渲染this.loc',this.state.loc);
      return <div onClick={this.updateLoc} style={{height:'50px',border:"1px solid pink"}}>内容</div>
    }
  }
  ReactDOM.render(
  <Example/>,
  document.getElementById('example')
);
```
