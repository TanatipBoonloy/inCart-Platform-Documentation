# JS

## Components
> Component ทุก component ต้องรับ classname และมี static-classname เป็นของตัวเอง แล้ว apply ให้กับ component ในรูปแบบ
> ```js
> className={cx(<static-classname>, ...<own-classname> ,<props-className>)}
> ```
> เสมอ

## HOCs
> การทำ HOCs ต้องรับ {...rest} จาก props แล้วส่งให้ ComposedComponent เสมอ
> ```jsx
> render() {
>   const { <hoc-props>, ...rest } = this.props
>   return <ComposedComponent {...<pass-to-composed-component-props>} {...rest} />
> }
> ```
