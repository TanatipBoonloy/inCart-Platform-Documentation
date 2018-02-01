# CSS
การทำ Theming และ Style สามารถใส่ style ได้ใน level ต่างๆ ซึ่ง style ที่ apply กับ component จะถูก override ตามน้ำหนักของแต่ละ level เรียงลำดับตาม priority สูงสุด ไปยัง ต่ำสุด

> หากต้องการเพิ่มน้ำหนักของ Style ที่แต่ละ level ทำได้โดยเพิ่ม hierarchy ของ classname ที่เกิดขึ้น

## TOC
* [META inline](#meta-inline)
* [CMS style](#cms-style)
  * [inCart Component](#incart-component-style)
  * [Semantic Component](#semantic-component-style)
* [Component style](#component-style)
  * [CSS](#css-1)
  * [Theming](#theming)
* [Global style](#global-style)

## META inline
เป็นการทำ styling ผ่าน JSON โดยใส่เป็นรูปแบบ css in js มีน้ำหนักสูงที่สุด
> inline style ที่จะถูก apply เฉพาะ specific component (เมื่อแก้ style จาก CMS UI จะถูก apply ที่นี่)<br/>
> [ตัวอย่างการใช้งาน](#style)

## CMS Style
สร้าง styling ผ่านไฟล์ sass ของแต่ละ component ที่ CMS server
> ใช้สำหรับใส่ style ที่แตกต่างกันในแต่ละร้าน
ข้อกำหนด
* สร้างไฟล์ตามชื่อ component ใน cms/themes/\<store\>
* ชื่อไฟล์เป็น CamelCase ใช้นามสกุล sass ในรูป <component-name>.sass เช่น SpecialButton.sass
* ในไฟล์ sass สามารถใส่ css/sass (คล้ายรูปแบบที่เขียนใน emotion/style-component ) ได้ทันที
### inCart Component Style
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

### Semantic Component Style
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

## Component Style
เป็นการสร้าง basic style สำหรับ component โดยจะเขียน style ลงใน js file ของ component นั้นๆ (รูปแบบ emotion-js/ styled-components)
> ใช้สำหรับสร้าง style ของ inCart Platform

### CSS
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
### Theming
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

## Global Style

style กลาง ซึ่งเป็นพื้นฐานของ incart platform มีน้ำหนักต่ำที่สุด
**ห้ามแก้ไข style ที่นี่**
