rc-form的大致流程，如果想彻底看懂的话，需要你能接受函数式编程。

```js
createForm(options)，返回了一个Form装饰器，然后只要传入表单Form。
createForm(options)(Form)再用getFieldDecorator装饰Field。

import createBaseForm from './createBaseForm';

export const mixin = {
  getForm() {
    return {
      getFieldsValue: this.fieldsStore.getFieldsValue,
      getFieldDecorator: this.getFieldDecorator,
      validateFields: this.validateFields,
      ...一系列表单相关的接口
    };
  },
};

function createForm(options) {
  return createBaseForm(options, [mixin]);
}

export default createForm;

此时createBaseForm生成一个options配套的FieldStore装饰器，createForm做的事只是传一个mixin（不理解这个词的话，你只需要知道mixin对象里的东西都会出现在最后React.Compoent实例的方法库中）。getForm把这些接口暴露出来给UI。

// 这是最终返回的React UI 的render的部分代码 位于createBaseForm.js
render() {
  const {...restProps } = this.props;
  const formProps = {
    [formPropName]: this.getForm(),
    // formPropName默认为'form'，可以在前面的option对象的formPropName字段里修改
  };
  const props = mapProps.call(this, {
    ...formProps,
    ...restProps,
  });
  return <WrappedComponent {...props}/>;
},
```

antd示例代码，你现在应该知道this.props.form是从哪里来的了，Form是已经被包装过的

作为“前朝老臣"的mixin虽然在react中慢慢被遗弃了，Composition vs Inheritance – React。但是面对对象的概念，对于程序员而言还是有必要了解的。我也搜到了一篇不错的文章。原来是直接继承在原型上，现在高阶组件是用props（即通过参数）来传递原来想拓展的继属性，通过栈来保存，再进一步肤浅地说就是，原来是增加业务代码的高度，现在是增加业务代码的宽度。

```js
// mixin
const target1 = new React.Component()
target.additionMethod1 = xxx
target.additionMethod2 = yyy
target.additionMethod3 = zzz

// 高阶组件
const target2 = new React.Component()
addAdditionMethod(xxx)(yyy)(zzz)(target2)
```