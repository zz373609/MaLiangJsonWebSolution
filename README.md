# Web JSON 解决方案

## Pre-request

- react
- react-router

## We awlays want to make a solution which build web as soon as possiable. That is reason we have react, Vue such as freamwork. Since those freamwork has already done a lot of works for us, we may have some way to have a config json and describe how web perform.

# What I want to do

have a json to describe all things of web such as views, events, styles.

So now we know what we need in our JSON

- views
- style
- events

## views

view is just like html, since we use react. View is just a component of react.

## styles

styles are just style of html, since we use react, then we just use inline style of html.

## event

about events we need think about couple situation.

1. sync event but only happen in current component

```
Class A extend Component {
    constructor(props){
        super(props)
        this.state = {
            a:1
        }
    this.change = this.change.bind(this)
    }

    change(){
        this.setState({
            a:2
        })
    }

    render(){
        return <div onClick={this.change}>{this.state.a}</div>
    }
}
```

1. async event but only happen in current component
   
```
Class B extend Component {
    constructor(props){
        super(props)
        this.state = {
            a:1
        }
    this.change = this.change.bind(this)
    }

    change(){
        callbackend.then(res=>{
            this.setState({
                a:res
            })
        })
    }

    render(){
        return <div onClick={this.change}>{this.state.a}</div>
    }
}
```

3. event bewteen components what if when you do something in A, but you want change something in B.

```
Class A extend Component {
    constructor(props){
        super(props)
        this.state = {
            a:1
        }
    this.change = this.change.bind(this)
    }

    change(){
        B.setState({
            b:10
        })

        //can you actully doing this, look like you can not, but actully you can in some way 
    }

    render(){
        return <div onClick={this.change}>{this.state.a}</div>
    }
}
```

I know it is a little confuse, but at least you will know that your json need those informations for views

```
{
    id:1 // easy to find the component
    meta:{
        "name":A // what component you will use
    },
    html:{
        props:{
            // the props need to pass to component
        },
        css: {
            style:{
                // custom styles
            }
        }
    }

}

```

I belive use these information you definatlly can render a component out.

Now let us talk about the hard part, events.

1. event

The problem:

- how to describe the event in json
- how to manage event bewteen the two component 

when you need to change something in the component, what you need to know is that what payload this event need, which component is going to be changed, which property in the component is going to be changed.

so the json need those information


```
{
    id:1 // easy to find the component
    meta:{
        "name":A // what component you will use
    },
    html:{
        props:{
            // the props need to pass to component
        },
        css: {
            style:{
                // custom styles
            }
        }
    },
    control:{
        action:{
            onClick:[
                {
                    endpoint:{
                    Webserver:{
                          "host": "www.test.com",
                          "method": "post"
                        }
                    },
                    payload:[
                        {
                            id:1,
                            path:A.state.a
                            key: b
                        }
                    ],
                    response:[
                        {
                            id:2,
                            path:B.state.b,
                            key:response.b.c
                        }
                    ]
                }
            ]
        }
    }

}

```

let me expain what happen here

you have a event onClick in Component A which id is 1

when you click this event in Component A you find eveything in payload, which is component with id 2 and the A.state.a(the component A's state's property a), and set it to b. as

```
{
    b:A.state.a
}
```

Then, send the 

```
{
    b:A.state.a
}
```
To server "www.test.com" with method "post"

Then when you got the response 

```
{
    response
}
```

you can find the value in the response.b.c and set back the component with id 2 with path B.state.b

all here is talking about is that, you find the payload you need in any component and send it to server, and get the value set back to any component you need to set by id and path. 

By this json you definatlly good enough to describe most action of website. of course we need something like window.location.herf = otherwebsite, some function like this. Will talk about this next chapter.

This is not stupid json idea to bullshit, since we have already done the code and more than 10 project by this way to describe the web by json, and it is really fast to make web once you build the system. Laterly, if we can make the project as a project easy to use, we will public the code and let eveyone to use it and try.

Since my English is not pefect, I will expain berifly by Chinese here.

整个项目的想法其实就是用一条json 从路由到事件交互如何描述出来，介于我们用的react，那我们网页的最小集合就是组件.

在json里面每一个组件都有一个id用来方便查找，json里面描述用哪一个组件，组件的样式都是很简单的。

难点在于如何描述网页的事件, 在react中我们知道,你需要做的就是，组件自己内部的管理，内部管理json不需要参与，因为我们的最小集是组件，json需要考虑的是组件之间的通讯。

这里我们可以这样考虑:实际上组件间的通讯就是 你有一个input 进入一个黑盒子比如后台，后台在给你一个output你找到对应的组件然后去修改对应的属性.

简单来说就是在组件  A  中拿到需要的payload，然后给后台,后台处理以后，返回response你再找到对应的组件  B  赋值到对应的属性上面。我认为网页的任何交互最后的抽象都可以抽象成这样。 所以json只需要有 payload，reponse 组件id。

这并不是一个凭空的想法，我们已经使用这个方法再十个以上的项目上面，再一些情况一下，能很高效的提高网站搭建的速度。如果之后通过一段时间的优化，能开放出来成为一个开源项目，是我们的一个目标.
