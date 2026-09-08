---
layout: blog_post
title: "Docker for MLOps: From Application Code to Reproducible Deployment"
date: 2024-10-17 20:00:00 +0700
categories: [MLOps, Engineering]
tags: [docker, containers, deployment, docker-compose, reproducibility]
description: "Ghi chú từ extra class về Docker, image, container, Dockerfile, Docker Compose và Docker Hub trong quy trình MLOps."
---

{% assign slide_deck = '/assets/data/blog/8-mlops-docker.pdf' | relative_url %}

> Đây là bài ghi chú được mình rút trích từ extra class **Docker - MLOps** của AI VIETNAM. Mình tập trung vào trực giác đằng sau container, cách Docker đóng gói ứng dụng và vì sao Docker Compose hữu ích khi một hệ thống có nhiều service.

📎 [Mở toàn bộ slide seminar (PDF)]({{ slide_deck }})

## Vấn đề không nằm ở việc chạy được code một lần

Một ứng dụng machine learning thường có nhiều thành phần: frontend, backend, database, model runtime, file cấu hình và các thư viện hệ thống. [Slide 4-10]({{ slide_deck }}#page=4) mô tả các thành phần này từ lúc development đến deployment.

Ở máy cá nhân, ta có thể khởi động frontend, backend và database bằng những lệnh riêng. Nhưng khi chuyển sang máy test hoặc production, từng server lại cần được config lại. Khác biệt về operating system, version thư viện, environment variable hoặc network có thể khiến một project chạy tốt ở máy này nhưng lỗi ở máy khác.

Đây là insight quan trọng từ các sơ đồ đầu phần introduction: deployment không chỉ là copy source code lên một server. Ta cần đóng gói cả môi trường mà code phụ thuộc vào.

## VM và container khác nhau thế nào?

[Slide 11-14]({{ slide_deck }}#page=11) so sánh cách tiếp cận cũ dùng virtual machine với container method.

<figure class="blog-slide">
  <img src="{{ '/assets/images/blog/slides/blog-08-slide-14-container-method.jpg' | relative_url }}" alt="So sánh virtual machine và container method" loading="lazy">
  <figcaption>Slide 14: container đóng gói application nhẹ hơn VM bằng cách dùng chung operating system host.</figcaption>
</figure>

Với VM, mỗi máy ảo thường mang theo một operating system riêng, chạy trên hypervisor. Cách này tạo ra isolation tốt nhưng cũng có overhead đáng kể. Nếu một server cần chạy nhiều ứng dụng nhỏ, việc lặp lại cả một guest operating system cho từng ứng dụng sẽ lãng phí tài nguyên.

Container chia sẻ kernel của host nhưng tách biệt process, filesystem, network và các dependency cần thiết. Hình container method trong slide trực quan ở điểm này: nhiều application được đóng gói riêng nhưng cùng chạy trên một nền tảng operating system.

Container không phải là VM thu nhỏ. Container nhẹ hơn và khởi động nhanh hơn, nhưng isolation và cách quản lý tài nguyên cũng khác. Chọn cách nào phụ thuộc vào mức độ isolation, portability và yêu cầu vận hành của hệ thống.

## Docker cung cấp những gì?

Docker được giới thiệu như một platform để develop, ship và run application. Mục tiêu thực tế là giảm khoảng cách giữa lúc viết code và lúc chạy code trong production.

### Docker architecture

[Slide 17-21]({{ slide_deck }}#page=17) minh họa Docker architecture với Docker CLI, Docker daemon và Docker Engine. CLI nhận lệnh từ người dùng, daemon lắng nghe Docker API và quản lý các Docker object như image, container, network và volume.

Ta có thể hình dung luồng cơ bản như sau:

```text
Docker CLI -> Docker Engine -> image / container / network / volume
```

Điểm hay của architecture này là người dùng không cần thao tác trực tiếp với mọi chi tiết hệ thống. Ta mô tả ý định bằng command, còn engine thực hiện việc build, tạo và quản lý container.

### Network và volume

Một container hiếm khi sống một mình. [Slide 23-25]({{ slide_deck }}#page=23) cho thấy frontend và backend có thể giao tiếp qua Docker network, còn volume dùng để giữ dữ liệu bên ngoài lifecycle của container.

Network giúp service gọi nhau bằng một không gian kết nối được quản lý rõ ràng. Volume giải quyết một vấn đề khác: container có thể bị xóa hoặc tạo lại, nhưng database, log và file upload thường không được mất theo container.

Đây là khác biệt giữa **ephemeral compute** và **persistent data**. Container có thể thay đổi thường xuyên; dữ liệu quan trọng cần một nơi lưu trữ có lifecycle riêng.

## Từ Dockerfile đến Docker image

Dockerfile là một text file chứa các instruction để build image. [Slide 27-30]({{ slide_deck }}#page=27) giải thích rằng mỗi instruction tạo ra một layer trong image.

Một Dockerfile cơ bản thường dùng:

- `FROM` để chọn base image;
- `RUN` để chạy lệnh trong quá trình build;
- `WORKDIR` để đặt thư mục làm việc;
- `COPY` để đưa source code vào image;
- `EXPOSE` để mô tả port mà application lắng nghe;
- `CMD` để đặt command chạy khi container khởi động.

Image là template bất biến để tạo container. Container là một instance đang chạy của image, bao gồm code, runtime, library và system tool cần thiết cho application.

Mô hình này tạo ra một ranh giới rõ ràng giữa build time và run time. Ta build image một lần, kiểm tra image, sau đó chạy cùng image ở nhiều môi trường khác nhau.

## Build hiệu quả hơn

Slide cũng giới thiệu multi-stage build và secret trong quá trình build. Multi-stage build cho phép dùng một stage đầy đủ compiler hoặc development dependency để build, rồi chỉ copy artifact cần thiết sang stage cuối.

Kết quả là production image nhỏ hơn, ít dependency thừa hơn và giảm bề mặt attack. Đây là một tối ưu quan trọng trong MLOps vì image machine learning thường có thể rất lớn.

Secret cần được xử lý cẩn thận. API key hoặc credential không nên được ghi cứng vào Dockerfile hay source code. [Slide 35-36]({{ slide_deck }}#page=35) đưa ra cách truyền secret trong build context thay vì để secret xuất hiện trong layer cuối.

## Run, debug và expose container

Sau khi có image, ta tạo container bằng `docker run`. Các thao tác thường gặp gồm:

```bash
docker run -it -p 8000:8000 backend:v0.0.0
docker exec -it backend_cont bash
docker logs backend_cont
```

`docker exec` giúp mở một command bên trong container đang chạy. `docker logs` giúp kiểm tra output của process. Đây là hai lệnh rất hữu ích khi container khởi động được nhưng application bên trong chưa hoạt động đúng.

Port mapping nối port trên host với port trong container. Nếu backend lắng nghe ở port 8000 trong container, ta cần map nó ra host để client có thể gọi tới.

Bind mount và volume giải quyết việc chia sẻ dữ liệu giữa host với container. [Slide 45-48]({{ slide_deck }}#page=45) dùng thư mục log và image để minh họa: file trong host có thể được nhìn thấy từ path tương ứng bên trong container.

## Khi có nhiều container: Docker Compose

Một ứng dụng thực tế có thể gồm frontend, backend, database và một worker. Chạy từng container bằng command riêng sẽ nhanh chóng trở nên khó nhớ và khó đồng bộ.

Docker Compose cho phép định nghĩa toàn bộ stack trong một file YAML. [Slide 49-56]({{ slide_deck }}#page=49) minh họa cách khai báo service, network và volume, sau đó khởi động hoặc dừng cả hệ thống bằng:

<figure class="blog-slide">
  <img src="{{ '/assets/images/blog/slides/blog-08-slide-50-docker-compose.jpg' | relative_url }}" alt="Docker Compose quản lý nhiều container, network và volume" loading="lazy">
  <figcaption>Slide 50: Docker Compose gom nhiều service, network và volume vào một application stack có thể quản lý cùng nhau.</figcaption>
</figure>

```bash
docker compose up -d
docker compose down
```

Insight của sơ đồ Compose là các container không còn là những process rời rạc. Chúng trở thành một application stack có cấu hình chung, network chung và lifecycle có thể quản lý cùng nhau.

## Docker Hub và việc chia sẻ image

Docker registry là nơi lưu trữ Docker image, còn Docker Hub là một public registry phổ biến. [Slide 57-61]({{ slide_deck }}#page=57) trình bày flow login bằng token, tag image theo username, push image lên Hub và pull image về máy khác.

Flow này biến image thành một artifact có thể phân phối:

```text
Dockerfile -> build image -> tag -> push registry -> pull -> run
```

Trong pipeline MLOps, artifact có thể tiếp tục đi qua test, deployment và monitoring. Vì vậy, Docker image không chỉ tiện cho developer. Nó còn là một đơn vị versioning và handoff giữa development, CI và production.

## Kết

Docker giải quyết một vấn đề rất đời thường nhưng rất quan trọng: làm sao để application mang theo môi trường cần thiết để chạy ổn định ở nhiều nơi.

Những hình trong slide nối thành một chuỗi logic:

1. VM đóng gói cả hệ điều hành và có overhead lớn hơn.
2. Container đóng gói application cùng dependency trong một môi trường nhẹ hơn.
3. Image là artifact được build từ Dockerfile.
4. Container là image đang chạy, có network và storage riêng.
5. Compose quản lý nhiều container như một stack.
6. Registry giúp chia sẻ image giữa các máy và các bước của pipeline.

Với MLOps, Docker không tự động làm cho model tốt hơn. Nó làm cho model và service dễ tái lập, dễ chuyển môi trường và dễ đưa vào quy trình vận hành hơn.
