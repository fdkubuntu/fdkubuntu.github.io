# fdkubuntu.github.io

Personal GitHub Pages site for **fdkubuntu**, based on the Flexible Jekyll theme.

## Hướng dẫn post bài

Đăng bài mới lên GitHub Pages repo `fdkubuntu/fdkubuntu.github.io`.

### Yêu cầu

- tạo bài mới trong `_posts/` theo tên `YYYY-MM-DD-slug.markdown`
- dùng front matter chuẩn:

```yaml
---
layout: post
title: ...
date: YYYY-MM-DD HH:MM:SS +0000
description: ...
img: filename.jpg
tags: [tag1, tag2]
---
```

- nếu có ảnh thumbnail, đặt ảnh trong `assets/img/` và `img:` chỉ ghi tên file

### Ràng buộc quan trọng

- KHÔNG dùng `date` ở tương lai, nếu không Jekyll/GitHub Pages sẽ không hiển thị bài
- ưu tiên thay đổi tối thiểu: chỉ thêm post + ảnh liên quan
- không tự ý sửa `_config.yml`, layout, pagination, `index.html` trừ khi cần sửa lỗi rõ ràng

### Sau khi làm

- kiểm tra front matter hợp lệ
- kiểm tra ảnh tồn tại đúng đường dẫn
- xem lại diff để chắc không đụng file thừa
- commit và push lên `origin main`
- báo lại commit hash và xác nhận GitHub Pages đang/will rebuild

### Nếu bài không hiển thị

Kiểm tra trước tiên `date`, `layout: post`, vị trí file trong `_posts/`, và file ảnh có tồn tại không
