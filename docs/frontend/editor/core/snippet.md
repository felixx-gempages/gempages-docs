# Snippet

## Snippet là gì?

Snippet là đơn vị setting nhỏ nhất. Nó xuất hiện ở leftSidebar và điều khiển giá trị của các Element.

Bạn có thể thấy danh sách các Snippet cơ bản bên dưới.

> [!note]
> Bạn tham khảo folder: app>"form" để tìm thấy tất cả các snippet. Vì nhiều snippet được tạo ra cố định cho các element khác nhau nên không tiện liệt kê

## Thuộc tính mở rộng

### Responsive Mode

```json
"mode": "responsive-mode",
```

Thuộc tính này sẽ giúp hiển thị icon responsive trên sidebar của editor

### Visible

```json
"visible": "field-hide-all field-visible-lg",
```

Đoạn code trên thể hiện 2 ý tưởng:

- Ẩn tên toàn bộ các màn hình
- Hiển thị trên màn hình lg

Bạn có thể sử dụng thêm các fields:

- field-hide-all
- field-visible-lg
- field-visible-md
- field-visible-sm
- field-visible-xs

### Desc (Mô tả)

```json
"desc": "If you wish to style particular content element differently, then use this field to add a class name and then refer to it in your css file.",
```

### Requires

#### Trường hợp 1: Sử dụng Requires đơn giản

```json
[
  {
    "name": "preview",
    "id": "preview",
    "class": "gf_to-switch",
    "caption": "Enable",
    "type": "input",
    "default_value": "1"
  },
  {
    "name": "page_quantity",
    "id": "page_quantity",
    "caption": "Page Quantity",
    "type": "input",
    "default_value": "10",
    "requires": "preview|1"
  }
]
```

Thuộc tính đã sử dụng:

```json
"requires": "preview|1",
```

Ý nghĩa: Nếu **snippet có id là preview có giá trị 1** thì **snippet có id là page_quantity sẽ hiển thị** và ngược lại

#### Trường hợp 2: Sử dụng với nhiều cặp giá trị

```json
"requires": "customKey|1+default-map|0",
```

Các cặp giá trị sẽ được ngăn cách bằng dấu "+" và có dấu "|" ngăn cách giữa các cặp giá trị

#### Trường hợp 3: Sử dụng với nhiều giá trị

```json
"requires": "trigger|afterAFewSeconds:scroll:userExit:scrollBottom",
```

Các giá trị sẽ được ngăn cách bằng dấu ":"

### Group snippets

Thuộc tính **items** và type **group** giúp nhóm các snippet với nhau

```json
{
  "caption": "Swatch Text",
  "type": "group",
  "mode": "responsive-mode",
  "items": [
    {
      "name": "swatch-text",
      "id": "swatche-text",
      "class": "gf_to-switch",
      "type": "text",
      "default_value": "1",
      "desc": "Show swatch text."
    },
    {
      "name": "swatch-hide",
      "id": "swatch-hide",
      "caption": "Only Hide Field",
      "type": "select",
      "options": {
        "All": "All",
        "Size": "Size",
        "Material": "Material",
        "Other": "Other"
      },
      "default_value": "All"
    }
  ]
}
```

### Advanced snippets

Thuộc tính **advanced** giúp tạo group advanced setting.

Ngoài ra, mode **responsive-mode-advanced** giúp modal có thể hỗ trợ responsive

```json
{
  "mode": "responsive-mode-advanced",
  "advanced": [
    {
      "name": "font-size",
      "id": "font-size",
      "class": "gf_to-range",
      "caption": "Font Size",
      "type": "input",
      "min": "0",
      "max": "100",
      "step": "1",
      "unit": "px",
      "default_value": "16px"
    },
    {
      "name": "color",
      "id": "color",
      "class": "gryff-color",
      "caption": "Color",
      "type": "text",
      "default_value": "#2d313f"
    }
  ]
}
```

## Danh sách

### Input

```json
{
  "name": "weeklabel",
  "id": "weeklabel",
  "caption": "Label",
  "type": "text",
  "default_value": "Weeks",
  "placeholder": "Weeks"
}
```

### Textarea

```json
{
  "name": "successMessage",
  "type": "textarea",
  "caption": "Success Message",
  "default_value": "Added to cart! [cart label=View Cart] or [continue label=Continue Shopping]."
}
```

### Range

```json
{
  "name": "font-size",
  "id": "font-size",
  "class": "gf_to-range",
  "caption": "Font Size",
  "type": "input",
  "min": "0",
  "max": "100",
  "step": "1",
  "unit": "px",
  "default_value": "16px"
}
```

### Color

```json
{
  "name": "color",
  "id": "color",
  "class": "gryff-color",
  "caption": "Text Color",
  "type": "text",
  "default_value": ""
}
```

### Switch

```json
{
  "name": "buttonHover",
  "id": "buttonHover",
  "class": "gf_to-switch",
  "type": "text",
  "default_value": "0",
  "caption": "Hover Options"
}
```

### Select

```json
{
  "name": "titleTag",
  "id": "titleTag",
  "caption": "Title Tag",
  "type": "select",
  "options": {
    "h1": "H1",
    "h2": "H2",
    "h3": "H3",
    "h4": "H4",
    "h5": "H5",
    "h6": "H6",
    "p": "Paragraph"
  },
  "default_value": "h3"
}
```

### Align

```json
{
  "name": "align_xs",
  "id": "align_xs",
  "class": "gf_to-align",
  "caption": "Alignment",
  "type": "select",
  "options": {
    "left": "Left",
    "center": "Center",
    "right": "Right"
  },
  "default_value": "center"
}
```

### Segment

```json
{
  "name": "modeLink",
  "id": "modeLink",
  "class": "gf_to-segment",
  "caption": "",
  "type": "input",
  "default_value": "auto",
  "dataValue": "auto,custom",
  "columns": "Auto,Custom"
}
```

Nếu muốn chọn nhiều giá trị thì sử dụng thuộc tính

```json
 "multiple": "true",
```

Thêm thuộc tính để bắt buộc cần chọn ít nhất một giá trị

```json
 "disable": "false",
```

Sử dụng với Icon. Lưu ý: bạn cần đăng ký icon trong code scss ở file: "gryffSegment.scss"

```json
{
  "name": "compare-style",
  "id": "compare-style",
  "class": "gf_to-segment",
  "caption": "Font style",
  "type": "input",
  "default_value": "",
  "dataValue": "bold,italic",
  "columns": "icon-bold,icon-italic",
  "disable": "true",
  "multiple": "true",
  "icon": "true"
}
```

### Datepicker

```json
{
  "name": "dateEvergreen",
  "id": "dateEvergreen",
  "class": "gf_to-datePicker",
  "caption": "Date",
  "type": "input",
  "default_value": "3h"
}
```

### Description

```json
{
  "type": "span",
  "class": "ui-dform-span set_des",
  "html": "<a href='#' class='reference'>Notice</a>: This element will not show correct information until a collection is picked in a Product List."
}
```

## Snippet phụ trợ

### Product

```json
[
  {
    "name": "product",
    "id": "product",
    "caption": "Product",
    "type": "text",
    "default_value": "Auto"
  },
  {
    "name": "pid",
    "id": "pid",
    "caption": "Product Id",
    "type": "text",
    "default_value": "auto",
    "disabled": "disabled",
    "desc": "This field is automatically generated.",
    "visible": "field-hide-all"
  },
  {
    "name": "pHandle",
    "id": "pHandle",
    "caption": "Product Handle",
    "type": "text",
    "default_value": "auto",
    "disabled": "disabled",
    "desc": "This field is automatically generated.",
    "visible": "field-hide-all"
  }
]
```

### Collection

```json
[
  {
    "name": "collection",
    "id": "collection",
    "caption": "Collection",
    "type": "text",
    "default_value": "All Collections"
  },
  {
    "name": "cId",
    "id": "cId",
    "type": "text",
    "caption": "Collection ID",
    "default_value": "auto",
    "disabled": "disabled",
    "desc": "This field is automatically generated."
  },
  {
    "name": "cHandle",
    "id": "cHandle",
    "caption": "Collection Handle",
    "type": "text",
    "default_value": "latest-products",
    "disabled": "disabled",
    "desc": "This field is automatically generated."
  }
]
```

### Blog

```json
[
  {
    "name": "blog",
    "id": "blog",
    "caption": "Blog",
    "type": "text",
    "default_value": "",
    "placeholder": "Choose blog..."
  },
  {
    "name": "bId",
    "id": "bId",
    "caption": "Blog ID",
    "type": "text",
    "default_value": "",
    "disabled": "disabled",
    "desc": "This field is automatically generated."
  },
  {
    "name": "bHandle",
    "id": "bHandle",
    "caption": "Blog handle",
    "type": "text",
    "default_value": "",
    "disabled": "disabled",
    "desc": "This field is automatically generated."
  }
]
```

### Article

```json
[
  {
    "name": "article",
    "id": "article",
    "caption": "Article",
    "type": "text",
    "default_value": "latest-article"
  },
  {
    "name": "aId",
    "id": "aId",
    "caption": "Article Id",
    "type": "text",
    "default_value": "auto",
    "disabled": "disabled",
    "desc": "This field is automatically generated."
  },
  {
    "name": "aHandle",
    "id": "aHandle",
    "caption": "Article Handle",
    "type": "text",
    "default_value": "auto",
    "disabled": "disabled",
    "desc": "This field is automatically generated."
  }
]
```

### Pick Link

```json
{
  "name": "editLink",
  "id": "editLink",
  "class": "editLink",
  "caption": "Hero Link",
  "type": "text",
  "placeholder": "https://your-shop.myshopify.com",
  "default_value": ""
}
```

### Icon

```json
{
  "name": "choose_icon",
  "id": "choose_icon",
  "class": "gf_to-iconPicker",
  "caption": "Choose Icon",
  "type": "input",
  "default_value": "PGkgY2xhc3M9ImdmX2ljb24gZmEgZmEtZmxhc2giPjwvaT4="
}
```

### Date

```json
{
  "name": "datetime",
  "id": "datetime",
  "class": "gf_to-datePicker",
  "caption": "Date",
  "type": "text",
  "default_value": "2010/12/31 00:00:00"
}
```

### Thêm data-compile

Dùng khi không muốn render liquid. Dành cho app thứ 3

```json
{
  "name": "compile",
  "id": "compile",
  "type": "text",
  "default_value": "false",
  "visible": "field-hide-all"
}
```

### Swatcher

```json
{
  "name": "swatcherv1",
  "id": "swatcherv1",
  "caption": "Swatch Rules <small style=\"font-style: italic;font-size: 12px;font-weight: 300;\">(Ability to define custom image and color for each swatch)</small>",
  "type": "text",
  "default_value": "e30="
}
```
