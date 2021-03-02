# Editor Convention

## Cách đặt tên

Toàn bộ tên trong chương trình đều sẽ theo một chuẩn chung là **camelcase**

Trong Editor đã cài đặt eslint để bắt lỗi từ tên hàm, tên biến... Nhưng tên file thì mọi người cần tự định nghĩa theo chuẩn

### Tên hàm

```javascript
const isBlank = () => {};
```

### Tên biến

```javascript
let productImage = "https://";
```

Nếu là dom của trình duyệt hoặc của jQuery thì sẽ dùng từ khóa $ phía trước

```javascript
let $body = jQuery("body");
let $product = document.querySelector(".product");
```

### Tên Folder

```bash
/folder-name
```

### Tên file

```bash
/file-name.js
```

## Quản lý Store

Store sẽ dùng để lưu chữ toàn bộ thông tin của Editor.

Các đặc tính của store:

- Store được xây dựng dựa trên những framework khác như: Redux, Mobx
- Store chỉ tồn tại từ khi reload đến khi tắt ứng dụng
- Store được chia thành nhiều modules nhỏ bên trong để dễ quản lý
- Store cũng dùng coding conversion của Editor
- Store có thể sử dụng làm trigger events qua lại giữa các khu vực hoặc function
- Store được lưu trữ vào window, hiểu sâu hơn thì tồn tại trong Ram của khách hàng
- Store có hai khu vực và hoàn toàn riêng biệt: 1 store trong Editor, 1 store trong Iframe giả lập

Mỗi modules có 3 phần chính:

- state
- getter
- setter

### State

Tạo state

```javascript
/
 * State
 */
export const state = {
    __shop: null as TypeShop
};
```

Một vài lưu ý:

- key của state cần được đặt khác nhau ở những modules
- Nên gắn kiểu của value để có thể xem được các thông tin bên trong của state

### Getter

```javascript
/**
 * Hàm lấy version của shop theo ngày tạo
 * @returns version: 20200310 (năm: 2020, tháng: 03, ngày: 10)
 */
export const getShopVersionInStore = ():number => {
    const shop = store.getValue("__shop");
    const createdAt = (shop as TypeShop)?.createdAt;

    let date = new Date().toISOString().slice(0, 10);
    if (createdAt) {
        date = new Date(createdAt).toISOString().slice(0, 10);
    }
    date = date.replace(new RegExp(/-/, "gm"), "");
    return parseInt(date);
};
```

Một số lưu ý

- Tên hàm cần có chữ "get" phía trước để biết được là getter
- Cần có ký tự "InStore" ở cuối để phân biệt được với các hàm thông thường

### Setter

```javascript
/**
 * Hàm cập nhật một fields trong state của store
 * @param newPlan plan mới của khách
 * @returns kết hoàn thành hoặc không
 */
export const setPlanShopInStore = (newPlan: string): boolean => {
    const shop = store.getValue("__shop") as TypeShop;
    if (shop) {
        shop.plan = newPlan;
        store.setValue("__shop", shop);
        return true;
    }
    return false;
};
```

Một số lưu ý

- Tên hàm cần có chữ "set" phía trước để biết được là setter
- Cần có ký tự "InStore" ở cuối để phân biệt được với các hàm thông thường

## Viết function trong utils

### Quy ước

Các hàm trong utils có một số đặc điểm sau:

- Hàm có thể tái sự dụng ở nhiều nơi
- Hàm đơn giản để có thể viết unit test
- Hàm chỉ có chức năng chuyên biệt
- Hàm chỉ nhận đầu vào và đầu ra cố định
- Trong hàm không lấy hoặc đọc bất kỳ dữ liệu từ đâu khác
- Mỗi file là những chức năng riêng biệt. Không biết quá nhiều hàm vào một file duy nhất

### Ví dụ

```javascript
/**
 * Hàm kiểm tra image này có phải là của shopify không
 * @param src url image
 * @returns đúng hoặc sai
 */
export const hasImageShopify = (src: string): boolean => {
  if (!src) {
    return false;
  }
  if (src.indexOf("cdn.shopify.com/s/files/") != -1) {
    return true;
  } else if (src.indexOf("apps.shopifycdn.com/") != -1) {
    return true;
  }
  return false;
};
```

## Unit Test

### Ví dụ

file: **replaceImageToSize.ts**

test: **replaceImageToSize.test.ts**

File test sẽ cùng tên và và thêm prefix ".test" ở cuối file để định nghĩa unit test

```javascript
import { expect } from "chai";
import replaceImageToSize from "./replaceImageToSize";

describe("Replace Image To Size", () => {
  it("Kiểm tra cơ bản", () => {
    const src =
      "https://cdn.shopify.com/s/files/1/0136/8460/4987/products/3e00eb40dc68de54291913afcb7d9c6e.jpg?v=1544428532";
    const size = "100x";
    const success =
      "https://cdn.shopify.com/s/files/1/0136/8460/4987/products/3e00eb40dc68de54291913afcb7d9c6e_100x.jpg?v=1544428532";
    const result = replaceImageToSize(src, size);
    expect(result).to.equal(success);
  });
  it("Test ảnh có thêm ký tự -jpg", () => {
    const src =
      "https://cdn.shopify.com/s/files/1/0136/8460/4987/products/3e00eb40dc68de54291913afcb7d9c6e-jpg.jpg?v=1544428532";
    const size = "100x";
    const success =
      "https://cdn.shopify.com/s/files/1/0136/8460/4987/products/3e00eb40dc68de54291913afcb7d9c6e-jpg_100x.jpg?v=1544428532";
    const result = replaceImageToSize(src, size);
    expect(result).to.equal(success);
  });
});
```

Run:

```bash
yarn test
```

## Sử dụng events trình duyệt

### Namespace & on, off events

```javascript
jQuery("body")
  .off("click.switch")
  .on("click.switch", () => {});
```

**switch**: chính là namespace của event click. Từ đó khi mình off event thì chỉ off event của riêng namespace này từ đó dẫn đến không bị mất events của các sự kiện khu vực khác

Tại sao cần off trươc khi on. Vì trong editor mình thường xuyên bị lặp lại nhiều lần do reload nên nếu không off thì sẽ gây ra trường hợp chạy vào callback function nhiều lần do tồn tại đồng thời nhiều sự kiện

## Comment hàm

Mỗi hàm tạo ra luôn luôn cần comment

```javascript
/**
 * Hàm kiểm tra image này có phải là của shopify không
 * @param src url image
 * @returns đúng hoặc sai
 */
export const hasImageShopify = (src: string): boolean => {
  return false;
};
```

Comment cần những thông tin sau:

- Mô tả hàm
- Mô tả cho biến params
- Mô tả cho kết quả trả về
