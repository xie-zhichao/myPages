---
title: Ant Design Pro - 实践React Hooks - 组件
date: 2019-08-09 16:18:45
tags:
  - JavaScript
  - React
  - React Hooks
  - Ant Design
categories: JavaScript
---
## 需求
后台项目，使用Ant Design Pro, 有这样一个需求：有一个表格，宽度是自适应的，表格中有一列是备注，文本长度不定，我们希望在文本过长的时候，使用省略样式（ellipsis），同时鼠标悬浮时有提示框展示完整文本。

## 设计
我计划设计一个React Hooks组件，嵌在表格里面，实现文本的自适应省略样式。

### 单元格宽度
这一列我们只能使用相对宽度，因为整个表格是自适应宽度的，如果用固定宽度，可能在大屏上，这一列显得很窄。  

**这里我用百分比，同时在页面组件维护一个宽度状态，在mounted之后，按百分比计算这一列的宽度并更新状态，如：clientWidth * 0.2。另外，监听window resize事件，更新宽度状态。**

### 组件宽度
列宽计算出来之后，会通过props传给组件，有了宽度，利用：**text-overflow: ellipsis;** 就可以实现动态宽度的文本省略了。

### 提示框
这个提示框是在超长时才有，不超长时是没有的。这个是比较麻烦的一点，因为你要知道当前是不是在超长省略状态，我们需要这个状态来设置是否加提示框。  

为了实现这个功能，**我加了两个Hooks状态：是否超长、文本真实宽度。**
- **文本真实宽度**：渲染完成后，通过dom获取元素宽度。
- **是否超长**：比较文本真实宽度和组件的宽度。

## 实现
这里我就直接贴代码了，在后面会理一下关键点。
```javascript
export default function LineEllipsis(props) {
  const { children, width = '200px', ...restProps } = props;
  const [textWidth, setTextWidth] = useState(0);
  const [isOverflow, setIsOverflow] = useState(false);
  const textRef = useRef(null);
  const textStyles = {
    width,
    display: 'inline-block',
    overflow: 'hidden',
    wordWrap: 'nowrap',
    textOverflow: 'ellipsis',
  };

  useEffect(
    () => {
      if (textRef) {
        const { current } = textRef;
        const clientWidth = current.clientWidth;
        setTextWidth(clientWidth);
        if (!isOverflow && clientWidth > parseInt(width)) {
          setIsOverflow(true);
        } else if (isOverflow && clientWidth < parseInt(width)) {
          setIsOverflow(false);
        }
      }
    },
    [children]
  );

  useEffect(
    () => {
      if (textRef && textWidth > 0) {
        if (!isOverflow && textWidth > parseInt(width)) {
          setIsOverflow(true);
        } else if (isOverflow && textWidth < parseInt(width)) {
          setIsOverflow(false);
        }
      }
    },
    [width]
  );

  const textRender = () => {
    return (
      <span
        ref={textRef}
        style={isOverflow ? textStyles : { display: 'inline-block' }}
        {...restProps}
      >
        {children}
      </span>
    );
  };

  return (
    <div style={{ width }}>
      {isOverflow ? (
        <Popover content={<pre className={styles.pop}>{children}</pre>}>{textRender()}</Popover>
      ) : (
        textRender()
      )}
    </div>
  );
}
```
### 关键点：
- span的样式，要设置为inline-block，方便取到文本宽度。
- 文本可能会更新，所以需要监听children对象，变化时更新文本宽度、是否超长。
- 组件宽度是根据props参数动态适应，所以也需要监听，变化时要更新是否超长的状态。

## 总结
第二次使用React Hooks，确确实实感受到了好处。
- userEffect的依赖设置非常灵活好用。
    - 不设置，每次更新都会触发。
    - 设置为空，只在第一次加载时触发。
    - 设置为其他状态、或props中的状态时，只在这些状态变化时触发（***注意，依赖为对象时，不会深比较**）。
- 得益于useState, useEffect的用法灵活，Hooks组件写法上确实简洁不少。
