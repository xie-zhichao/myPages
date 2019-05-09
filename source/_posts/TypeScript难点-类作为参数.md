---
title: TypeScript难点-类作为参数
date: 2019-05-09 16:24:40
tags:
 - TypeScript
 - JavaScript
categories: TypeScript
---
## 需求场景
- 当我们用工厂模式去生产实例化对象时，我们传给工厂方法的就是个类型定义或方法
- 代理，我们需要代理方法去实例化对象

下面我们分别看看着两种场景，在TypeScript中如何去实现：

### 工厂模式
```javascript
class Animal {
  
}

class Bee extends Animal {
 
}

class Lion extends Animal {
  
}

// 这里还是个单例模式
class AnimalFactory {
  private static instance: AnimalFactory;

  private constructor() {};

  static getInstance(): AnimalFactory {
    if(!AnimalFactory.instance) {
      AnimalFactory.instance = new AnimalFactory();
    }

    return AnimalFactory.instance;
  }

  // 注意看这个方法的参数
  create<A extends Animal>(c: new () => A): A {
    return new c();
  }
}
```

分析一下：

这里的参数c是：new () => A类型，表示c是一个类，而不是类实例

### 代理
```javascript
interface PageProps {
  component: typeof React.Component,
  pending: boolean,
  logged: boolean,
  [propName: string]: any
};

class IndexRouter extends React.Component<PageProps> {

  render() {
    const { component: Component, pending, logged, ...rest } = this.props
    
    return (
      <Route {...rest} render={props => {
        if (pending) return <div>Loading...</div>
        return logged
          ? <Component {...props} />
          : <Redirect to="/auth/login" />
      }} />
    )
  }
}

```

分析一下：
这里的component，是一个React组件类，而非组件实例。声明接口时，typeof表示取React.Component这个类的类型。
**上面工厂模式中，不能用这钟方式，我的理解是：typeof取不到泛型的类型。**