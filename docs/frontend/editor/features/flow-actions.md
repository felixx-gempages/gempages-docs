## Flow Actions là gì?

Là chức năng nâng cao của Gempages. Cho phép khách hàng có thể tạo được chuỗi các hành động trên trang bán hàng. Làm tăng trải nghiệm bán hàng cũng như khả năng chuyển đổi.

**Ví dụ:**

- Người mua hàng sau khi Add To Cart thành công, lúc này Popup sẽ hiển thị (lời cám ơn, product upsell, cart button, ...)
- Click vào button có thể popup rồi mở video. Sau khi hết video tự động đóng popup...
- Hover vào ảnh thì run animation
  <p align="center">
    <img src="../../assets/images/frontend/editor/flow-actions/customer-expect.png" />
    <br/> Khách mong muốn hiện popup sau khi add to cart
  </p>

## Ý Tưởng Code

1. Mỗi Atom sẽ có 1 list FlowActions
2. Mỗi FlowAction sẽ có 1 list Targets
3. Mỗi Target sẽ có 1 list Actions

Frontend chỉ lưu dữ liệu cần thiết.
Editor sẽ tự lấy dữ liệu hiển thị (VD: Label)

Data sau khi export ra frontend sẽ được lưu vào window.\_\_gfFlowActions

```json
[
  {
    "source": "#e-1601020728868",
    "events": ["click"],
    "targets": [
      {
        "id": "#e-1601020728868",
        "actions": [
          {
            "key": "bounce",
            "type": 1,
            "data": {
              "direction": "in-left",
              "delay": 0,
              "duration": 500,
              "screens": ["lg", "md", "sm", "xs"]
            }
          }
        ]
      }
    ],
    "sourceTarget": ".gf_button"
  }
]
```

Action sẽ được gọi thông qua **GEMSTORE**

## Atom Custom Actions

**Register custom action:**

Mỗi khi init Atom (MER). Cần add custom action của Atom đó vào trong list thông qua store.

```javascript
//add custom action for Video Bg
if (label == "Video Bg") {
  //set action
  store.setValue(keyAddAction, {
    key: "playVideo",
    type: 3,
    name: "Play Video",
    description: "",
    atomLabel: "Video Bg",
    settings: [delay],
  });
  store.setValue(keyAddAction, {
    key: "stopVideo",
    type: 3,
    name: "Stop Video",
    description: "",
    atomLabel: "Video Bg",
    settings: [delay],
  });
  store.setValue(keyAddAction, {
    key: "pauseVideo",
    type: 3,
    name: "Pause Video",
    description: "",
    atomLabel: "Video Bg",
    settings: [delay],
  });
}
```

Quy tắc thêm settings:

1. Range Picker

```javascript
{
  key: "delay",
  name: "Delay",
  value: 0,
  min: 0,
  max: 2000,
  step: 100,
  valueType: "ms",
  type: "range"
}
```

2. Checkboxex

```javascript
{
  key: "screens",
  name: "Trigger on",
  type: "checkbox",
  value: ["lg", "md", "sm", "xs"],
  options: [
    {
      value: "lg",
      text: "Desktop"
    },
    {
      value: "md",
      text: "Laptop"
    },
    {
      value: "sm",
      text: "Tablet"
    },
    {
      value: "xs",
      text: "Mobile"
    }
  ]
}
```

3. Select

```javascript
{
  "key": "direction",
  "name": "Direction",
  "value": "in-left",
  "type": "select",
  "options": [
    {
      "value": "in-left",
      "text": "Left In"
    },
    {
      "value": "in-right",
      "text": "Right In"
    },
    {
      "value": "in-up",
      "text": "Top In"
    }
  ]
}
```

Thêm code lắng nghe action ở frontend:

```javascript
jQuery(function () {
  var $module = jQuery("#m-{{module-id}}").children(".module");
  var mode = jQuery(".gryffeditor").hasClass("editing") ? "dev" : "production";
  if (mode == "production") {
    jQuery(window).ready(function () {
      $module.gfVideoBg();
    });
  } else {
    $module.gfVideoBg();
  }

  if (window.GEMSTORE) {
    window.GEMSTORE.subscribe("action-#m-{{module-id}}", function (key, data) {
      switch (key) {
        case "pauseVideo":
          $module.trigger("pausevideo");
          break;
        case "playVideo":
          $module.trigger("playvideo");
          break;
        default:
          break;
      }
    });
  }
});
```
