---
title: 从零开始实践React 高阶组件(HOC)
date: 2020-01-14 15:46:38
tags:
  - JavaScript
  - React
  - Hooks
  - 高阶组件
categories: JavaScript
---
## 高阶组件的定义
`HOC高阶组件`，概念来源于函数式编程中的`高阶函数`，高阶函数的定义是：入参是函数，并且返回函数的函数；类推，高阶组件定义就是：入参是组件，并且返回组件的函数。==强调一下，它是一个函数==。

我们用`TypeScript`来描述一下单参数的高阶组件：

> 因为高阶组件来源于函数式编程，所以本文的示例会采用函数式的写法。

```javascript
type HOC = (Fc: React.FC) => React.FC
```

有了类型系统，设计上就会清晰规范很多。

## 高阶组件的用途

高阶组件是一个函数，它会返回一个新的组件，这样有什么用呢？

我们来看Web前端最基本的场景：我们有一个图书列表，需要用Ajax请求数据，然后在页面上把它展现出来。

我们现在基于React框架，从最简单的方案开始，看看整个进化过程：

### 方案1.0 - 简单直接

第一种方案最直接：在组件mounted之后，执行Ajax请求，待取到数据之后，更新状态，触发组件更新，渲染数据到UI上面。

核心代码是这样的：

```javascript
const BookList = () => {
  const [bookList, setBookList] = useState([]);

  useEffect(() => {
    Ajax.get('data/books').then(res => setBookList(res));
  }, []);

  return (
    <ul>
      {bookList.map(book => (
        <li>{book.name}({book.author})</li>
      ))}
    </ul>
  );
};
```

这个方案跑起来没啥问题，不过略显粗糙，没有分层，耦合太重。如果我们要加图书评论列表、相似图书推荐列表等等功能，那么就要再写几个看起来差不多的组件。模型和状态部分的代码几乎是重复的。

==重复的代码肯定是可以抽象出来的==，来看下一个方案。

### 方案2.0 - 高阶组件

高阶组件可以传入一个无状态组件，返回一个新组件，那么我们是不是可以把通用的模型和状态封装在新组件里面，通过子组件的形式把模型状态通过`props`传入被包装的组件，这样就达到抽象通用功能的目地。

核心代码很简单：

```javascript
const WithModel = (WrappedComponent, modelPath) => {
  const WrappedComponentWithModel = props => {
    const [data, setData] = useState(null);

    useEffect(() => {
      Ajax.get('data/' + modelPath).then(res => setData(res));
    }, []);

    return <WrappedComponent {...props} data={data} />;
  }

  
  return WrappedComponentWithModel;
};

const BookList = props => {
  const { data: bookList } = props;

  return (
    <ul>
      {(bookList || []).map(book => (
        <li>{book.name}({book.author})</li>
      ))}
    </ul>
  );
};

const BookComments = props => {
  const { data: bookComments } = props;

  return (
    <ul>
      {(bookComments || []).map(comment => (
        <li>{comment.content} from {comment.author}</li>
      ))}
    </ul>
  );
};

const BookListWithModle = WithModel(BookList);
const BookCommentsWithModle = WithModel(BookComments);
```

这样重构之后，是不是感觉架构提升了一个层次，我们可以很方便地在模型层增加功能，比如Ajax响应结果的检查。

**还存在待改进的地方**：
- 现在可以查询数据了，如果还需要发送数据怎么办？比如添加一条图书记录、添加一条新的评论。
- 如果另外一个组件也要展示图书列表，那两个组件里面的数据是不是就重复了？
- 如果两个组件都展示图书列表，但是用两份数据，那么一个组件刷新了，会不会两个组件展示的数据不一致？
- 还有一个隐藏问题，如果在Ajax请求数据时，组件销毁了，状态还会更新吗？会有问题吗？

如何解决上述问题？我们来看3.0版：

### 方案3.0 - 高阶组件工厂

高阶组件工厂，参考自设计模式中的工厂模式，我们通过给定的设置让组件工厂去生产组件，是方案2.0的进一步抽象。

看核心代码，重点地方会给出注释说明：

```javascript
import React, { useState, useEffect } from 'react';

export function useModelStore() {
  const Ajax = (() => {
    const books = [{ name: 'Book1', content: 'About book', author: 'Alex' }];

    const getBooks = () => {
      return new Promise(resolver => {
        setTimeout(resolver(books), 200);
      });
    };

    const addBook = () => {
      return new Promise(resolver => {
        const No = Math.round(Math.random() * 1000);
        books.push({ name: `Book${No}`, content: `About book${No}`, author: 'Alex' });
        setTimeout(resolver(200), 200);
      });
    };

    return {
      get: getBooks,
      post: addBook,
    };
  })();

  // 这里先定义一个图书Model Hooks函数，用于工厂创建Model Hooks
  const BookModelHooks = initialState => {
    const [bookList, setBookList] = useState(initialState);
    const [pending, setPending] = useState(false);

    const load = async params => {
      setPending(true);
      Ajax.get('data/books', params)
        .then(res => setBookList(res))
        .finally(() => setPending(false));
    };

    const addBook = async params => {
      Ajax.post('data/books/add', params).then(() => load());
    };

    // 返回Model相关的state和state操作
    return [bookList, { pending, load, addBook }];
  };

  // 顶层状态仓库
  const ModelStore = () => {
    let $store = {};

    const createStore = (modelName, ModelHooks, initialState) => {
      $store = Object.assign({}, $store, { [modelName]: ModelHooks(initialState) });
    };

    const getStore = modelName => $store[modelName];

    return { createStore, getStore };
  };

  const modelStore = ModelStore();
  modelStore.createStore('bookModel', BookModelHooks, []);

  return modelStore;
}

// 这里就是高阶组件工厂，工厂会生产出一个可以注入图书Model的高阶组件
const WithModelFactory = modelName => {
  // 返回高阶组件
  return WrappedComponent => {
    const WrappedComponentWithModel = props => {
      const { modelStore, ...passThroughProps } = props;

      return <WrappedComponent {...passThroughProps} model={modelStore.getStore(modelName)} />;
    };

    return WrappedComponentWithModel;
  };
};

const BookList = props => {
  const { model } = props;
  const [bookList, { load, addBook }] = model || [];

  useEffect(() => {
    load && load();
  }, []);

  return (
    <>
      <ul>
        {(bookList || []).map(book => (
          <li>
            {book.name}({book.author})
          </li>
        ))}
      </ul>
      <button type="button" onClick={addBook}>Add+</button>
    </>
  );
};

const BookComments = props => {
  const { model } = props;
  const [bookList] = model || [];

  return (
    <ul>
      {(bookList || []).map(comment => (
        <li>
          {comment.content} from {comment.author}
        </li>
      ))}
    </ul>
  );
};

export const BookListWithModel = WithModelFactory('bookModel')(BookList);
export const BookCommentsWithModel = WithModelFactory('bookModel')(BookComments);
```

看上去是不是有点像`Redux`，它确实用了相似的设计：`全局状态`，`状态注入`。

这个设计比2.0又进了一大步，它还可以继续优化，我们可以使用`Reducer`、`Context`等等，继续设计下去，就会越来越接近`Hooks Redux`了。

## 总结

到这里，我们已经从一个简单Hooks组件，进化为一个高阶组件工厂了，而且还有全局状态管理。用到了几种设计模式：装饰器模式(高阶函数，高阶组件其实就是装饰器)，工厂模式，单例模式(全局状态仓库)；
