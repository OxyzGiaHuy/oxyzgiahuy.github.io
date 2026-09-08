---
layout: blog_post
title: "Cloud Services for MLOps: Deploying and Monitoring Machine Learning Systems"
date: 2024-10-26 20:00:00 +0700
categories: [MLOps, Cloud]
tags: [cloud-computing, aws, google-cloud, deployment, ssh, grafana]
description: "Ghi chú từ extra class về cloud services, CLI, VM deployment, SSH và monitoring với Grafana trong quy trình MLOps."
---

{% assign slide_deck = '/assets/data/blog/9-mlops-cloud-deployment.pdf' | relative_url %}

> Đây là bài ghi chú được mình rút trích từ extra class **Cloud Services - MLOps** của AI VIETNAM. Mình tập trung vào các hình mô tả cloud, flow triển khai VM, kết nối SSH và cách biến metrics thành dashboard monitoring.

📎 [Mở toàn bộ slide seminar (PDF)]({{ slide_deck }})

## Cloud không chỉ là một máy chủ ở nơi khác

Cloud thường được mô tả như một mạng lưới các server ở nhiều nơi, với những chức năng khác nhau: lưu trữ dữ liệu, chạy application hoặc cung cấp một service. [Slide 4-9]({{ slide_deck }}#page=4) đi từ khái niệm cloud đến service provider, data center và cloud services.

Các provider được nhắc đến gồm AWS, Microsoft Azure, Google Cloud, Alibaba Cloud, IBM Cloud, Oracle Cloud và FPT Smart Cloud. Bản đồ data center trong slide giúp hình dung một điều quan trọng: cloud không phải một khối trừu tượng duy nhất. Phía sau một API là hạ tầng vật lý, network, storage và nhiều region khác nhau.

## Vì sao đưa hệ thống lên cloud?

[Slide 10]({{ slide_deck }}#page=10) tóm tắt sáu lý do:

- **Economy:** dùng tài nguyên theo nhu cầu thay vì tự mua toàn bộ phần cứng.
- **Security:** có sẵn nhiều cơ chế hạ tầng và dịch vụ bảo vệ.
- **Scalability:** có thể mở rộng hoặc thu nhỏ tài nguyên.
- **Focus:** team tập trung vào application và business logic.
- **Agility:** tạo và xóa resource nhanh hơn.
- **Maintenance:** cloud provider phụ trách một phần hạ tầng bên dưới.

Tuy nhiên, “cloud có security” không có nghĩa application tự động an toàn. Người dùng vẫn phải cấu hình identity, network, secret, permission và dữ liệu đúng cách. Cloud giúp cung cấp công cụ, còn trách nhiệm sử dụng công cụ vẫn nằm ở team triển khai.

### Chi phí là một phần của thiết kế

Cloud có thể giúp bắt đầu với chi phí thấp, nhưng resource chạy liên tục sẽ tạo ra hóa đơn liên tục. [Slide 13]({{ slide_deck }}#page=13) giới thiệu cloud calculator để ước lượng chi phí AWS, GCP hoặc Azure trước khi tạo resource.

Đây là một thói quen quan trọng khi làm MLOps: không chỉ hỏi model chạy được không, mà còn hỏi chạy bao lâu, trên loại máy nào và chi phí cho một request hoặc một training run là bao nhiêu.

## IaaS, PaaS và SaaS

Cloud service model giúp phân biệt phần nào do provider quản lý và phần nào do người dùng chịu trách nhiệm. [Slide 12]({{ slide_deck }}#page=12) đặt các service model trên một phổ từ infrastructure đến software.

Với IaaS, người dùng có quyền kiểm soát nhiều hơn nhưng cũng phải tự quản lý VM, OS, runtime và application. PaaS giảm bớt phần hạ tầng để team tập trung vào code. SaaS cung cấp một phần mềm hoàn chỉnh để người dùng sử dụng trực tiếp.

Không có model nào luôn tốt nhất. Một pipeline research cần GPU tùy chỉnh có thể phù hợp với VM. Một API ổn định, ít cần quản lý server có thể phù hợp với managed service. Quyết định nên dựa trên mức kiểm soát, chi phí và công sức vận hành.

## CLI là cách nói chuyện với server

Khi làm việc với cloud VM, command line interface thường là công cụ chính. [Slide 15-21]({{ slide_deck }}#page=15) giới thiệu shell, Bash, Z Shell và các lệnh cơ bản.

Một nhóm lệnh tối thiểu gồm:

```bash
pwd
ls
cd project
mkdir logs
cp config.example.yml config.yml
mv old-name new-name
grep error app.log
```

Các lệnh này không đặc biệt dành cho cloud, nhưng cloud khiến chúng trở nên quan trọng hơn. Khi SSH vào một VM không có giao diện đồ họa đầy đủ, navigation, đọc file, tìm log và quản lý process đều diễn ra qua terminal.

Slide cũng nhắc đến `chmod`, `history`, `whoami`, `htop`, `shutdown` và `reboot`. Đây là những building block nhỏ để kiểm tra user hiện tại, permission và trạng thái của máy.

## Triển khai VM trên cloud

Phần cloud deploy minh họa cả AWS Console và GCP Console. [Slide 24-32]({{ slide_deck }}#page=24) đi qua việc tạo GCP VM: chọn machine configuration, OS và storage, network rồi khởi tạo máy.

Ở phía AWS, [slide 33-35]({{ slide_deck }}#page=33) minh họa tạo EC2, cấu hình virtual server và launch. Dù giao diện hai provider khác nhau, các câu hỏi thiết kế khá tương tự:

<figure class="blog-slide">
  <img src="{{ '/assets/images/blog/slides/blog-09-slide-26-gcp-vm.jpg' | relative_url }}" alt="Giao diện cấu hình GCP virtual machine" loading="lazy">
  <figcaption>Slide 26: tạo VM là chuỗi quyết định về machine, OS, storage và access, không chỉ là một nút create.</figcaption>
</figure>

- VM cần bao nhiêu CPU, RAM hoặc GPU?
- Chọn OS và disk size nào?
- Port nào được mở ra ngoài?
- Ai được phép kết nối vào máy?
- Resource có cần tự động tắt khi không sử dụng không?

Những screenshot trong slide hữu ích ở chỗ chúng biến cloud thành một chuỗi lựa chọn cụ thể. Một VM không chỉ là nút “create”; nó là sự kết hợp của compute, storage, network và access control.

## SSH và VS Code Remote

Sau khi tạo VM, ta cần một cách kết nối an toàn. [Slide 36]({{ slide_deck }}#page=36) giới thiệu `ssh-keygen`, công cụ tạo cặp khóa cho SSH.

Luồng cơ bản là:

1. tạo private key và public key;
2. đặt public key ở server;
3. giữ private key ở máy cá nhân;
4. kết nối bằng SSH mà không phải truyền password mỗi lần.

Private key không nên commit vào Git hoặc gửi cho người khác. Nó giống như chìa khóa để vào server, nên permission và nơi lưu trữ cần được quản lý cẩn thận.

Các slide 37-42 giới thiệu VS Code SSH Remote. Hình ảnh cho thấy người dùng có thể mở thư mục trên VM trực tiếp trong VS Code, chỉnh sửa code và chạy lệnh từ xa như đang làm việc trên local machine.

Điều này hữu ích với ML vì môi trường có GPU thường nằm trên server. Ta có thể dùng laptop cho việc viết code và dùng VM cho training hoặc inference, nhưng vẫn giữ một workflow tương đối liền mạch.

## Monitoring với Grafana

Deploy xong chưa phải là kết thúc. Một service có thể đang chạy nhưng latency tăng, memory đầy hoặc request error tăng dần mà người dùng chưa báo lỗi.

[Slide 44-45]({{ slide_deck }}#page=44) giới thiệu Grafana như một nền tảng analytics và visualization cho monitoring, observability và alert. Sơ đồ đơn giản mô tả application gửi metrics hoặc dữ liệu tới backend, database rồi hiển thị trên Grafana.

<figure class="blog-slide">
  <img src="{{ '/assets/images/blog/slides/blog-09-slide-45-grafana.jpg' | relative_url }}" alt="Sơ đồ application, backend, database và Grafana trong cloud monitoring" loading="lazy">
  <figcaption>Slide 45: monitoring nối application với backend, database và Grafana để biến metrics thành dashboard có thể theo dõi.</figcaption>
</figure>

Dashboard trong slide cho thấy nhiều loại thông tin có thể đặt cạnh nhau: biểu đồ thời gian, số liệu tổng hợp, trạng thái hệ thống và threshold alert. Giá trị của dashboard không nằm ở việc làm giao diện đẹp, mà ở việc giúp team phát hiện sự thay đổi trước khi nó trở thành incident.

Trong một hệ thống ML, metrics có thể gồm latency, throughput, error rate, CPU, GPU, memory, queue length và cả model-specific signal như prediction distribution hoặc confidence. Monitoring tốt cần nối được technical metrics với chất lượng thực tế của model.

## Kết

Ba phần của seminar tạo thành một deployment loop:

```text
Cloud resource -> deploy application -> connect by SSH -> monitor service
```

Cloud cung cấp compute và các managed service. CLI giúp team làm việc với server. SSH tạo kênh truy cập an toàn. Grafana biến trạng thái hệ thống thành thông tin có thể quan sát và hành động.

Insight mình rút ra từ các sơ đồ và screenshot là MLOps không chỉ là đưa model lên một VM. Một hệ thống hoàn chỉnh cần được thiết kế cùng lúc về resource, permission, chi phí, network và monitoring. Khi các phần này được nối với nhau, model mới có cơ hội trở thành một service có thể vận hành lâu dài.
