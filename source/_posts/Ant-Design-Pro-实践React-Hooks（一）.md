---
title: Ant Design Pro-实践React Hooks（一）
date: 2019-08-02 11:51:10
tags:
  - JavaScript
  - React
  - React Hooks
  - Ant Design
categories: JavaScript
---
## 背景
React Hooks目前是React社区最炙手可热的新技术，我们准备追一下热度，在当前的项目中实践一下Hooks技术。  
我们的项目使用的脚手架是Ant Design Pro 2.0，初步想法是把现有的一个有状态页面组件重构成Hooks函数组件。

## 设计
动手之前，先理一下思路，用Hook函数重构的话，需要调整哪些部分：
- 组件类要拆解成Hooks函数
- 组件状态改成Hooks状态
- 类属性改成Hooks函数本地变量
- 类方法改成Hooks函数的嵌套函数，可公用的也可以作为Hooks函数平级的函数

再理一下过程中的问题：
- 函数和类不一样，它的this是由调用方决定的，是不确定的，所以不要使用this（**Hooks函数里面也没必要用this**）。
- 没有this，就需要用传参了，Hooks函数就是props转递。
- **Dva的使用**，Ant Design Pro使用的Dva来管理数据（里面封装的是Redux），这里我们还是可以继续使用，通过connect把Redux管理的model数据注入props。
    > 这里有个小知识点，组件类的model注入，使用了注解形式的装饰器，函数是不能使用装饰器的，所以需要我们手工注入。**理解一点：这里的装饰器就是高阶函数**。
- **状态共享**，Hooks状态如何共享给其他Hooks函数或普通函数？**Hooks状态声明时会返回：状态、修改此状态的方法，这两个返回值是可以传递和共享的**。
    > 这里也补充一个小知识，我们是可以声明一个服务类的Hooks，然后把状态和状态修改器返回来，这个是可以共享和传递的，可以实现类似全局状态管理器的功能，Hooks也提供了额外的辅助方法：useReducer。这里可以深入去学习一下。

解决了这些问题，我们就可以实践了。

## 实现
我们拆成了两个Hooks组件：分页列表 + 查询表单，来看看最终实现的代码。

### 入口 - 分页列表Hooks组件

```javascript
function TableList(props) {
  const {
    dispatch,
    account: { accountList },
    loading,
  } = props;

  const [formValues, setFormValues] = useState({});
  const [selectedRows] = useState([]);

  const columns = [
    {
      title: '序号',
      dataIndex: '_index',
    },
    {
      title: '账户名称',
      dataIndex: 'accountName',
    },
  ];

  function handleSearch(e) {
    e && e.preventDefault();
    const { form } = props;

    form.validateFields((err, fieldsValue) => {
      if (err) return;

      dispatch({
        type: 'account/accountList',
        payload: fieldsValue,
      });
    });
  }

  function handleStandardTableChange(pagination, filtersArg, sorter) {
    const filters = Object.keys(filtersArg).reduce((obj, key) => {
      const newObj = { ...obj };
      newObj[key] = getValue(filtersArg[key]);
      return newObj;
    }, {});

    const params = {
      pageNumber: pagination.current,
      pageSize: pagination.pageSize,
      ...formValues,
      ...filters,
    };
    if (sorter.field) {
      params.sort = sorter.order === 'ascend' ? 'asc' : 'desc';
    }

    dispatch({
      type: 'account/accountList',
      payload: params,
    });
  }

  useEffect(() => {
    dispatch({
      type: 'account/clear',
    });
    // 自动查询
    handleSearch();
  }, []);

  return (
    <PageHeaderWrapper>
      <Card bordered={false}>
        <div className={styles.tableList}>
          <div className={styles.tableListForm}>
            <SearchForm formValues={formValues} handleSearch={handleSearch} {...props} />
          </div>
          <StandardTable
            loading={loading}
            data={accountList}
            columns={columns}
            rowKey="_index"
            selectedRows={selectedRows}
            hideAlert
            disablePagination={false}
            onChange={handleStandardTableChange}
          />
        </div>
      </Card>
    </PageHeaderWrapper>
  );
}

const tableList = connect(({ accountaging, loading }) => ({
  accountaging,
  loading: loading.models.accountaging,
}))(Form.create()(TableList));

export default tableList;

```
这里我们注意几个点：
- props我们是透传给列表查询表单的。
- 查询方法也是传过去的。
- 存放表单值的状态是声明在列表组件，传给表单组件。
- 这里的userEffect实现了之前ComponentDisMount的功能，注意：**依赖传入了一个空数组，这里表示不依赖任何状态，所以只会在初次加载时执行，否则会出现死循环**。
- 组件导出前，有个model和form的注入。

### 列表查询表单

```javascript
function SearchForm(props) {
  const {
    account: { accountList },
    form,
    formValues,
    handleSearch,
  } = props;
  const { getFieldDecorator } = form;

  return (
    <Form onSubmit={handleSearch} layout="inline">
      <Row gutter={16} justify="start">
        <Col xl={8} md={12} sm={24}>
          <FormItem labelCol={{ span: 6 }} wrapperCol={{ span: 16 }} label="账户名称">
            {getFieldDecorator('accountName', {
              initialValue: '',
            })(<Input placeholder="账户名称" />)}
          </FormItem>
        </Col>
        <Col xl={8} md={24}>
          <Button className="fun-button" type="primary" htmlType="submit">
            查询
          </Button>
        </Col>
      </Row>
    </Form>
  );
}
```
表单组件这块，东西不多，理解props就好。

## 想法
其实整个实现下来，并不难。但是还有些问题需要我们考虑一下：
- 使用Hooks之后，带来了哪些改变？
- 代码逻辑是不是更优了？
- 代码结构是否清晰？
- 什么样是最佳实践？

留待讨论。
