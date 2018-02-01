# CSS Table of Contents

การทำ styling ใน incart-platform มีอยู่ด้วยกัน 4 ระดับ เรียงลำดับตาม priority ต่ำสุด ไปยัง สูงสุด

> หากต้องการเพิ่มน้ำหนักของ Style ที่แต่ละ level ทำได้โดยเพิ่ม hierarchy ของ classname ที่เกิดขึ้น

## TOC
* [Global style](#global-style)
* [Component style](#component-style)
  * [Semantic](#Semantic component)
  * [Theming](#theming)
* [META inline](#meta-inline)
* [CMS style](#cms-style)
  * [inCart Component](#incart-component-style)
  * [Semantic Component](#semantic-component-style)
  
## Global style
การ override ระดับ global จะกระทบทั้งเว็บไซต์
ตัวอย่าง
* การตั้งค่า font-family
* การ reset default style ของ html (h1, h2, …, a)

สามารถดูโค้ดได้ที่
> @store-front / helpers / globalStyles

## Component Style
เป็น style กลางของ component หรือเรียกว่า default style (ร้าน inCart)

จะเริ่มทำ เมื่อมีการสร้าง component ใหม่ โดย pattern ที่ใช้มี 2 กรณี คือ
##### 1. Semantic component
เราจะสร้างไฟล์ {Component}.style.js ไว้ในโฟล์เดอร์เดียวกัน เช่น ต้องสร้าง Card
 
```js
molecules/
    Card/
      index.js
      Card.js
      Card.style.js
```
ในไฟล์ Card.style.js จะเอา css ของ semantic มาใส่
2.  Custom Component เช่น ColorSwatch, PriceSummary, AddressCard จะสร้าง css ไว้ใน file ที่เป็น react เลย
```js
import React from 'react'
import PropTypes from 'prop-types'
import { cx } from 'emotion'
import { css } from 'react-emotion'

const colorSwatchStyle = ({ value, selected }) => css`
    min-width: ${size};
    background: ${value};
    border-radius: 2px;
    height: ${size};
    display: flex;
    align-items: center;
    margin-left: 10px;
    position: relative;
    z-index : 1;
    
    :after {
      ${selected && `
      height: 46px;
      width: 46px;
      border: 1px solid #000000;
      top: -4px;
      left: -4px;
      content: "";
      border-radius: 2px;
      position: absolute;
      `
      }
    }
`

const ColorSwatch = (props) => {
  const onClick = (e, data) => props.onChange(data)
  return (
    <div
      className={cx(props.className, colorSwatchStyle(props), 'color-swatch')}
      onClick={onClick}
      key={props.value}
      role="button"
      aria-hidden
    />
  )
}
```



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