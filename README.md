# inCart Platform Code Technique and Pattern

Summarize all technique and pattern use in inCart Platform Project

## Table of Contents
* [JS](#js)
  * [Components](#components)
  * [HOCs](#hocs)
* [JSON](#json)
  * [\_meta](#_meta)
    * [hoc/hocs](#hochocs)
    * [context](#context)
    * [translate](#translate)
    * [slots](#slots)
    * [style](#style)
    * [hiddenOn](#hiddenon)
    * [useTheme](#usetheme)
    * [cautions](#cautions)
* [CSS](#css)
  * [META inline](#meta-inline)
  * [CMS style](#cms-style)
    * [inCart Component](#incart-component-style)
    * [Semantic Component](#semantic-component-style)
  * [Component style](#component-style)
    * [CSS](#css)
    * [Theming](#theming)
  * [Global style](#global-style)

## JS

### Components
> Component ทุก component ต้องรับ classname และมี static-classname เป็นของตัวเอง แล้ว apply ให้กับ component ในรูปแบบ
> ```js
> className={cx(<static-classname>, ...<own-classname> ,<props-className>)}
> ```
> เสมอ

### HOCs
> การทำ HOCs ต้องรับ {...rest} จาก props แล้วส่งให้ ComposedComponent เสมอ
> ```jsx
> render() {
>   const { <hoc-props>, ...rest } = this.props
>   return <ComposedComponent {...<pass-to-composed-component-props>} {...rest} />
> }
> ```

## JSON
JSON Schema สำหรับ component แบ่งเป็น 3 ส่วน
- component: ระบุชื่อ component ว่าเป็น component ใด
- props: ระบุ props ต่างๆ ที่ต้องการส่งให้ component
- \_meta: เป็นส่วนที่ใช้เสริมความสามารถต่างๆของ component นั้นๆ
```js
{
  "_meta": {
    "translate": {
      "content": "submit-button"
    }
  },
  "component": "Button",
  "size": "large",
  "primary": true
}
```
### \_meta

#### hoc/hocs
สามารถใส่ HOCs ให้ component เพื่อให้ทำงานตาม logic ต่างๆได้ โดยใส่ได้ 2 รูปแบบ คือ hoc ซึ่งรับ string และ hocs ซึ่งรับ array(string)
> ```js
> "hoc": <hoc-name>
> "hocs": [<hoc-name>, <hoc-name>, ...]
> ```
```js
// hoc example
"_meta": {
  "hoc": "withCartSpinner"
}
// hocs example
"_meta": {
  "hocs": ["withCartSpinner", "withLoader"]
}
```
**ข้อควรระวัง** [การใช้งาน ```\_meta: hoc``` คู่กับ ```\_meta: contexts```](#cautions)

#### context
คือการ apply HOCs withContextConsumer ให้กับ components โดย HOC นี้จะทำหน้าที่ map context ที่ได้รับเป็น props ให้กับ component
> ```js
> "context": {
>   <component-props>: <get-context-key>,
>   ...
> }
> ```
```js
// example
"_meta": {
  "context": {
    "loading": "formLoading"
  }
}
```
**ข้อควรระวัง** [การใช้งาน ```\_meta: hoc``` คู่กับ ```\_meta: contexts```](#cautions)

#### translate

```js
"_meta": {
  "translate": {
    "validationErrors": {
      "isEmail": "validate-email-wrong-format"
    },
    "content": "email-input-label"
  }
}
```
#### slots
```js
"_meta": {
  "slots": {
    "trigger": {
      "component": "Button",
      "content": "Open Modal"
    }
  }
}
```
#### style
```js
"_meta": {
  "style": {
    "computer": {
      "background": "red"
    },
    "tablet": {
      "paddingLeft": "23px"
    },
    "mobile": {
      "position": "absolute !important",
      "bottom": 0
    }
  }
}
```
#### hiddenOn
```js
{
  "_meta": {
    "hiddenOn": ["mobile", "tablet"]
  },
  "component": "PaginationBar"
},
{
  "_meta": {
    "hiddenOn": ["computer"]
  },
  "component": "PaginationDropdown"
}
```
#### useTheme
```
  "_meta": {
    "useTheme": false
  }
```
#### cautions
ควรระวังในการใช้ context คู่กับ hocs เนื่องจากอาจทำให้มีปัญหาเรื่องของ flow ของ props
> เมื่อใช้ context กับ hocs คู่กันจะเกิด React Virtual Dom ในรูปแบบ
> ```js
> <ContextConsumer>           // \_meta: context
>   <HOCs>                    // \_meta: hoc
>     <ComposedComponent />   // component
>   </HOCs>
> </ContextConsumer>
> ```

## CSS
การทำ Theming และ Style สามารถใส่ style ได้ใน level ต่างๆ ซึ่ง style ที่ apply กับ component จะถูก override ตามน้ำหนักของแต่ละ level เรียงลำดับตาม priority สูงสุด ไปยัง ต่ำสุด

> หากต้องการเพิ่มน้ำหนักของ Style ที่แต่ละ level ทำได้โดยเพิ่ม hierarchy ของ classname ที่เกิดขึ้น


### META inline
เป็นการทำ styling ผ่าน JSON โดยใส่เป็นรูปแบบ css in js มีน้ำหนักสูงที่สุด
> inline style ที่จะถูก apply เฉพาะ specific component (เมื่อแก้ style จาก CMS UI จะถูก apply ที่นี่)<br/>
> [ตัวอย่างการใช้งาน](#style)

### CMS Style
สร้าง styling ผ่านไฟล์ sass ของแต่ละ component ที่ CMS server
> ใช้สำหรับใส่ style ที่แตกต่างกันในแต่ละร้าน
ข้อกำหนด
* สร้างไฟล์ตามชื่อ component ใน cms/themes/\<store\>
* ชื่อไฟล์เป็น CamelCase ใช้นามสกุล sass ในรูป <component-name>.sass เช่น SpecialButton.sass
* ในไฟล์ sass สามารถใส่ css/sass (คล้ายรูปแบบที่เขียนใน emotion/style-component ) ได้ทันที
#### inCart Component Style
สามารถ apply style ต่างๆ ได้โดยตรง
```scss
 // FormInput.sass
 color: white;
 &.red {
  color: red;
 }
 &.special {
  border-radius: 5px;
  border-color: goldenFlameOfTheHeaven;
  background-color: brightestLightOfTheWorld;
 }
```

#### Semantic Component Style
ในการ apply style ต้องใส่ className ตามรูปแบบของ Semantic เพื่อให้การ override style มีน้ำหนักมากกว่า Style ของ Semantic
```scss
  // Button.sass
  &.ui.button {
    border-radius: 0px;
    color:white;
    :hover {
      background: rgba(211, 91, 19, 0.9);
    }
    :disabled {
      color: grey;
    }
  }
  &.basic {
    border-radius: 0px;
    color: rgba(211, 91, 19, 1);
    :hover {
      box-shadow: none;
    }
  }
```

### Component Style
เป็นการสร้าง basic style สำหรับ component โดยจะเขียน style ลงใน js file ของ component นั้นๆ (รูปแบบ emotion-js/ styled-components)
> ใช้สำหรับสร้าง style ของ inCart Platform

#### CSS
Pattern สำหรับเขียน style สำหรับ component

ข้อกำหนด
* การใช้ style ของแต่ละ component ต้องใช้ผ่าน { css } แล้วนำ style ไป { cx } ที่ className props
* component ต้องรับ props className
* component ต้องมี static-classname เป็นของตัวเอง (string ที่บ่งบอกถึง component นั้นๆ)
* props className ของ component ได้จากการ { cx } className ต่างๆ เข้าด้วยกันโดย<br/>**className ที่รับจาก props ต้องอยู่หลังสุดเสมอ**

```js
  import React, { Component } from 'react'
  import { css } from 'react-emotion'
  import { cx } from 'emotion'
  import Button from '@atoms/Button'

  const redButtonStyle = css`
    background: red;
    color: white;
  `

  class RedButton extend Component {
    render() {
      const {
        className,
        ...rest
      } = this.props
      return (
        <Button className={cx('red-button', redButtonStyle, className)} {...rest} />
      )
    }
  }
```
#### Theming
การใช้ Theming จะใช้ withTheme ในการ inject colors / size  
ใช้ { css } โดยเรียกเป็น function ให้ส่ง theme จาก withTheme เข้ามา
```js
  import withTheme from '@store-front/hocs/withTheme'
  import { pickColor } from '@store-front/selectors/theme'

  const darkGreyButtonStyle = (theme) => css`
    background: ${pickColor(theme)('darkGrey')} // use pickColor to get color
    color: ${get(theme, ['colors', 'lightGrey'])} // get color directly from theme
  `
  const DarkGreyButton = (props) => {
    const { className, theme, ...rest } = props
    return (
      <Button
        className={cx(
        'grey-button',
        darkGreyButtonStyle(theme),
        className,
        )}
        {...rest}
      />
    )
  }

  export default withTheme(DarkGreyButton)
```

### Global Style

style กลาง ซึ่งเป็นพื้นฐานของ incart platform มีน้ำหนักต่ำที่สุด
**ห้ามแก้ไข style ที่นี่**
