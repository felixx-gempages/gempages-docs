# GemCache

## Gempages Caching Starter

### Link docs

[https://guides.rubyonrails.org/caching_with_rails.html](https://guides.rubyonrails.org/caching_with_rails.html)

Cache là bộ nhớ đệm chứa dữ liệu, các dữ liệu được nằm chờ yêu cầu từ ứng dụng hoặc phần cứng. Dữ liệu được chứa trong cache có thể là kết quả của tính toán trước đó, hoặc là sự trùng lặp dữ liệu được lưu trữ ở một nơi khác

Trong rails, rails sử dụng cache store làm nơi lưu trữ các thứ được cache

### ActiveSupport::Cache::Store

Là class khởi tạo cho các loại cache store, các methods có sẵn `read`, `write`, `delete`, `exist?`, and `fetch`

- fetch : Check if cache key exist then get, if not exist, assign data after excute block to cache key
- write: Write data to key
- read: Read cache value by key
- delete_matched : delete all cache value of key like
- delete: delete cache value by key
- exist?: check if data cache with key given exist?

Các cache store ở dưới đều khởi tạo từ class trên, cách config , trong file **production.rb/development.rb** (tùy môi trường mà rails sẽ khởi chạy với 2 file này)

```ruby
config.action_controller.perform_caching = true #enable cache

config.cache_store = { tên cache store và các option } #config loại cache store và các option liên quan
```

Ví dụ:

- Memory Store

  ```ruby
  config.cache_store = :memory_store, { size: 64.megabytes }
  ```

- File store

  ```ruby
  config.cache_store = :file_store, "/path/to/cache/directory"
  ```

- Redis Store

  ```ruby
  config.cache_store = :redis_cache_store, { url: ENV['REDIS_URL'] }
  ```

- Danga's memcached server store

  ```ruby
  config.cache_store = :mem_cache_store, "cache-1.example.com", "cache-2.example.com"
  ```

Config ENV cho dung lượng cache (không config mặc định 64mb)

```text
ENV['GEM_CACHE_SIZE'] = 64
```

Có thể access cache store qua phương thức

```ruby
Rails.cache
```

Nhưng trong GemPages đã được viết hết vào lib **GemCache** (libs/gem_cache.rb), tất cả đều sử dụng lại các phương thức của **Rails.cache**, và đã được autoload khi khởi chạy chương trình và (config autoload libs trong application.rb) mục đích để tiện nếu sau này sử dụng loại cache khác hoặc muốn cutomize function riêng cho cache

Nếu muốn sử dụng, chỉ cần gọi

```ruby
@fsCode = GemCache.fetch("system-setting/dashboardv3-full-story-code", expires_in: 12.hours) do
          SystemSetting.find_by_key("dashboardv3-full-story-code")
end
```

Đoạn code trên sẽ check key `system-setting/dashboardv3-full-story-code` trong cache, nếu không tồn tại value với giá trị trên thì sẽ thực hiện code block và return lại giá trị trong code block, đồng thời set giá trị trong cache_store với key là `system-setting/dashboardv3-full-story-code`, thời hạn expire 12 tiếng

### Các phương thức rails lưu trữ store phổ biến và cách access cache cơ bản , tóm tắt và lý do chọn cache store

#### ActiveSupport::Cache::MemoryStore

Cache trong bộ nhớ của process , là loại nhanh nhất và dễ dùng nhất, nhược điểm là từ process hoặc server khác không thể gọi sang nhau, mà chỉ có thể gọi từ process khởi tạo cache đó => sử dụng trong môi trường dev hoặc môi trường production mà khối lượng cache không nhiều / ít server

Bộ nhớ cache set cho loại store này là lấy từ ram của server, nếu data cache mà vuợt quá bộ nhớ cache => rails sẽ tự động clear data ( các key cũ nhất ) để có đủ memory cho cache mới

#### ActiveSupport::Cache::FileStore

Cache trên ổ đĩa cứng của server, dung lượng có thể lớn hơn so với cache trong bộ nhớ, và có thể access từ process trên cùng server, nhược điểm là chậm ( do không dùng ram để cache )

Đối với những ứng dụng lớn và có nhiều server khác nhau , khối lượng cache lớn hơn thì nên dùng 2 loại cache dưới

#### ActiveSupport::Cache::MemCacheStore

Memcached là một cached server đơn giản, cho phép bạn lưu trữ cặp key-value với dữ liệu đơn giản trong bộ nhớ => phù hợp với việc lưu trữ dữ liệu đơn giản, tốc độ cao hơn redis ( tuy nhiên trong các docs đều nói là nhanh hơn không quá nhiều )
Dữ liệu cache trên Memcached có thể truy cập từ các process/các server khác nhau

#### ActiveSupport::Cache::RedisCacheStore

RedisCacheStore giống Memcached , cũng lưu trữ bộ nhớ trong server riêng, có thể truy cập từ process trên server khác, nhưng redis khác ở điểm là một kiểu database trong bộ nhớ, có thể lưu trữ dữ liệu kiểu phức tạp hơn Memcached => trong các docs đều nói redis quản lý bộ nhớ hiệu quả hơn nếu lưu data theo dạng hash nên có thể dùng các phép toán phức tạp hơn, và redis clear bộ nhớ chiếm của cache cũng tốt hơn so với Memcached, trong các app lớn đều dùng redis là phần nhiều
Ngoài ra RedisCacheStore có thể backup lại dữ liệu cache , có thể config để restart mà không bị mất cache, nếu cache đầy có thể ghi tạm vào bộ nhớ swap , dễ scale và có nhiều tool support hơn ...

Đối với Gempages hiện tại, thì dữ liệu cache cũng chưa nhiều ( chỉ cache các data load sẵn trong dashboard / snippet / all templates và 1 số dữ liệu nhỏ khác ) nên team sẽ sử dụng cache
ActiveSupport::Cache::MemoryStore
là loại cache đơn giản, dễ sử dụng và quản lý cache key nhất cho Gempages nhằm giảm thiểu query đến database và tăng tốc độ load API

## Gempages Caching Usage

Link docs để hiểu rõ hơn các methods GemCache tái sử dụng lại

[https://api.rubyonrails.org/classes/ActiveSupport/Cache/Store.html](https://api.rubyonrails.org/classes/ActiveSupport/Cache/Store.html)

Tất cả cache đều access thông qua **Rails.cache**

Nếu muốn turn off cache tạm thời, thì chỉ cần gán

```ruby
Rails.cache = ActiveSupport::Cache::NullStore.new()
```

Lúc này rails sử dụng store cache là nullstore => không còn cache nữa, nếu muốn enable lại, gán lại bằng một instance memory cache store mới hoặc sử dụng chính instance cũ

```ruby
Rails.cache = ActiveSupport::Cache::MemoryStore.new()
```

### Các methods hiện có trong libs GemCache

- **fetch** : Check nếu tồn tại value cho cache key, nếu tồn tại, return giá trị, nếu không tồn tại, excute code block và return lại giá tri sau khi excute code block

  ```ruby
  @fsCode = GemCache.fetch("system-setting/dashboardv3-full-story-code", expires_in: 12.hours) do
    SystemSetting.find_by_key("dashboardv3-full-story-code")
  end
  ```

- **write**: Write data to key

  ```ruby
  GemCache.write("shop_snippet/#{shop.shopify_domain}",my_snippets, expires_in: 2.hours)
  ```

- **read**: Read cache value by key (nên dùng fetch sẽ tiện hơn)

  ```ruby
  GemCache.write("shop_snippet/#{shop.shopify_domain}")
  ```

- **delete_matched** : delete tất cả các key nếu có chứa ký tự

  ```ruby
  GemCache.delete_matched("dashboardv3/sections")
  ```

- **delete**: delete cache value by key

  ```ruby
  GemCache.delete("shop_snippet/#{shop.shopify_domain}")
  ```

- exist?: check if data cache with key given exist?

  ```ruby
  GemCache.delete("shop_snippet/#{shop.shopify_domain}")
  ```

- clear: clear all cache

  ```ruby
  GemCache.clear()
  ```

### Ví dụ về dữ liệu được cache trong GemPages bao gồm (và cách thức trigger để clear cache đó)

#### System Settings Cache

```text
Cache
- admin_controller.rb : cache lại code full story, key "system-setting/dashboardv3-full-story-code"
- application_controller.rb : cache @hotjarCode,@GoogleAnalytic, @fbPixel code, key "system-setting/hotjar-code","system-setting/google-analytic-code", "system-setting/facebook-pixel-code"
Trigger clear:
- system_setting_controller.rb tất cả đều là record của system settings, nên sẽ trigger clear cache khi update system settings
```

#### All template data cache

```text
api_controller : cache all template data,key  "templates/all/#{page.to_s}/#{sort}/#{order}"
clear_cache:  global_template_controller.rb GemCache.instance.delete_matched("templates/all/"), clear khi update/create/delete new global template
```

#### Section data

```text
section_controller.rb : cache section data in dashboard, trigger update cache when ( banner/suggestion/blog settings update)

sectionData = GemCache.fetch("dashboardv3/sections/#{@currentPlan}/#{negativeRemaining.to_s}", expires_in: 12.hours) do
  getSectionsData(@currentPlan,negativeRemaining)
end

trigger clear : section_open_controller.rb

def clear_cache
  GemCache.delete_matched("dashboardv3/sections")
end
```

#### Cache My Snippet

```text
snippets_service.rb : cache my_snippets, rewrite to cache when my snippet of shop update

my_snippets = GemCache.fetch("shop_snippet/#{shop.shopify_domain}", expires_in: 2.hours) do
  ShopSnippet.where(shop_id: shop.id).first
end
```

#### Cache All snippet và all category => để load snippet ko cần query db

```text
model snippet.rb
allSnippet = GemCache.fetch("snippets/all/expect-hide", expires_in: 2.hours) do
  Gempages::Snippets::Snippet.where("status <> 'hide'")
end

allCategories = GemCache.fetch("categories/all", expires_in: 2.hours) do
  Gempages::Snippets::Category.all
end

Trigger clear cache khi global snippet hoặc categories được create/update/delete

categories_controller.rb

def clear_cache
  GemCache.delete_matched("categories/all")
end

open/snippet_controller.rb

def clear_cache
  GemCache.delete_matched("snippets")
end

```
