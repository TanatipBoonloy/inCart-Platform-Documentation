# CSS Table of Contents

การทำ styling ใน incart-platform มีอยู่ด้วยกัน 4 ระดับ เรียงลำดับตาม priority ต่ำสุด ไปยัง สูงสุด

> หากต้องการเพิ่มน้ำหนักของ Style ที่แต่ละ level ทำได้โดยเพิ่ม hierarchy ของ classname ที่เกิดขึ้น

## TOC
* [Global style](#global-style)
* [New Component style](#component-style)
  * [Semantic](#1-semantic-component)
  * [Custom](#2-custom-component)
* [CMS style](#cms-style)
  * [inCart Component](#incart-component-style)
  * [Semantic Component](#semantic-component-style)
  * [Pattern Use cases](#Pattern-ในการเขียน)
    * [1. Varied spacing between row](#1-varied-spacing-between-row)
    * [2. Component that use ContextProvider](#2-component-that-use-contextprovider)
    * [3. Media Query](#3-media-query)
* [META inline](#meta-inline)
  
## Global style
การ override ระดับ global จะกระทบทั้งเว็บไซต์
ตัวอย่าง
* การตั้งค่า font-family
* การ reset default style ของ html (h1, h2, …, a)

สามารถดูโค้ดได้ที่
> @store-front / helpers / globalStyles

## Component Style
เป็น style กลางของ component หรือเรียกว่า default style (ร้าน inCart)
​
> !! ทุก component ต้องรับ prop className และใส่เข้า html tag !!
```js
const PriceSummary = ({ className }) => (
  <div className={cx()}>
    <ContextProvider>{children}</ContextProvider>
  </div>
)
```

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
##### 2. Custom Component
ยกตัวอย่างเช่น ColorSwatch, PriceSummary, AddressCard จะสร้าง css ไว้ใน file ที่เป็น react เลย

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
###### Theming
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


## CMS Style
นำ default component มาปรับ style ให้เข้ากับร้านนั้นๆ โดยสร้างไฟล์ {ComponentName}.scss ที่
```js
__mocks__
    cms
      themes
        {store}
          {ComponentName}.scss
```
* store = folder ร้าน
* componentName = ชื่อ component ซึ่งต้องตรงกับชื่อไฟล์ js ด้วย


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

### Pattern ในการเขียน
#### 1. Varied spacing between row
ต้องการจัด padding-top, bottom ของหลายๆ Row โดยที่แต่ละ Row มี padding ไม่เท่ากัน

Example: CartPage
```text
    CartPage
        Grid
            Row (padding-top: 30, bottom: 10)
                Column
                    Header
            Row (padding-top: 10, bottom: 20)
                Column
                    Text
            Row
                Column
                    ...
```
Case นี้ให้ใส่ className ลงไปในแต่ละ Row แล้ว override padding ผ่าน CartPage.scss
```text
    CartPage
        Grid
            Row className="cart-header"
                Column
                    Header
            Row className="cart-amount-items"
                Column
                    Text
            Row
                Column
                    ...
```
```scss
/* CartPage.scss */

  &.cart-page {
    .ui.grid {
      .row {
        &.cart-header {
          padding-top: 30px;
          padding-bottom: 10px;
        }
        &.cart-amount-items {
          padding-top: 10px;
          padding-bottom: 20px;
        }
      }
    }
  }    
```
#### 2. Component that use ContextProvider
กรณีที่ Component ดังกล่าวมีการเรียกใช้ ContextProvider ในการ render
Example: PriceSummary
```js
const PriceSummary = (props) => {
  const {
    priceSummary,
    children,
  } = props
  const price = new Price(priceSummary)
  return (
    <ContextProvider data={price.data}>
      {children}
    </ContextProvider>
  )
}
```
สำหรับ case นี้สิ่งที่ผิดคือ
* ไม่รับ className จาก prop ทำให้ไม่สามารถเขียน .scss ได้
* ไม่มี div ที่บ่งบอก className="price-summary"

```js
// Fixed By
const PriceSummary = (props) => {
  const {
    className,
    priceSummary,
    children,
  } = props
  const price = new Price(priceSummary)
  return (
    <div className={cx('price-summary', className)}>
      <ContextProvider data={price.data}>
        {children}
      </ContextProvider>
    </div>
  )
}
```
ทำให้สามารถเขียน PriceSummary.scss เพื่อ override css ของ Row ที่เขียนเป็น children อยู่ใน json ได้

#### 3. Media Query
การเขียน media query ในไฟล์ .scss
```scss
    @media only screen and (max-width: 767px) {
      // mobile only
      color: antiquewhite;
      ...other css
    }
    
    // we use mobile-first design, so default style should work with mobile
    
    color: red; // for mobile by default
    
    @media only screen and (min-width: 768px) and (max-width: 991px) {
      // tablet only
      color: antiquewhite;
    }
    
    @media only screen and (min-width: 992px) {
      // computer +
      color: black;
    }
```


## META inline
เป็นการทำ styling ผ่าน JSON โดยใส่เป็นรูปแบบ css in js มีน้ำหนักสูงที่สุด
> inline style ที่จะถูก apply เฉพาะ specific component (เมื่อแก้ style จาก CMS UI จะถูก apply ที่นี่)<br/>
> [ตัวอย่างการใช้งาน](#style)