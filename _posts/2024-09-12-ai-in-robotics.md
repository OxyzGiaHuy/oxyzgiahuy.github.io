---
layout: blog_post
title: "AI in Robotics: Perception, Learning, and Autonomy"
date: 2024-09-12 20:00:00 +0700
categories: [Robotics, Artificial Intelligence]
tags: [robotics, perception, robot-learning, vision-language-models, reinforcement-learning]
description: "Ghi chú từ seminar về các thành phần của robotics, robot learning, VLMs, swarm robotics và những thách thức khi xây dựng robot tự chủ."
---

{% assign slide_deck = '/assets/data/blog/7-ai-in-robotics.pdf' | relative_url %}

> Đây là bài ghi chú được mình rút trích từ seminar **AI in Robotics** của Tuan Dang. Slide đi từ những loại robot phổ biến đến các thành phần kỹ thuật như perception, decision making, learning, human-robot interaction và simulation.

📎 [Mở toàn bộ slide seminar (PDF)]({{ slide_deck }})

## Robotics không chỉ là chế tạo phần cứng

Khi nghĩ về robotics, nhiều người thường hình dung trước tiên về một cỗ máy có motor và chuyển động. Nhưng một robot hoạt động được trong môi trường thật cần nhiều lớp khác nhau: cảm nhận thế giới, định vị, lập kế hoạch, ra quyết định, thực hiện hành động và học từ dữ liệu hoặc phản hồi.

[Slide 2-5]({{ slide_deck }}#page=2) mở đầu bằng các ví dụ như humanoid robots, animal-inspired robots, robotaxi và self-driving vehicles. Phần tiếp theo giới thiệu IsaacSim, kinematics, actuators và multimodal sensors.

Điều này cho thấy robotics là một lĩnh vực giao giữa mechanical engineering, control, computer vision, machine learning và human-computer interaction. Một model tốt nhưng không kết nối được với sensor, actuator hoặc planner thì vẫn chưa tạo thành một robot hữu ích.

## Robot cần cảm nhận và hành động

### Kinematics, actuators và sensors

Kinematics mô tả quan hệ giữa chuyển động của các khớp và vị trí của robot. Với robot hai chân hoặc robot thao tác, kinematic optimization có thể được dùng để tìm chuyển động hợp lệ và tránh va chạm.

Actuator là phần biến quyết định của hệ thống thành chuyển động vật lý, chẳng hạn motor hoặc hydraulic actuator. Sensor lại cung cấp thông tin về môi trường và trạng thái của robot.

[Slide 7-8]({{ slide_deck }}#page=7) nhấn mạnh multimodality: robot có thể dùng camera, LiDAR, âm thanh và nhiều loại sensor khác. Hình ảnh ví von “five senses plus one” khá dễ hiểu, vì robot càng làm việc trong môi trường phức tạp càng khó chỉ dựa vào một nguồn dữ liệu.

### Perception

Perception là quá trình biến dữ liệu sensor thành thông tin mà planner hoặc controller có thể sử dụng. Các task có thể gồm 2D perception, 3D perception, vision, LiDAR processing và sensor fusion.

Nếu robot chỉ nhận ra vật thể trong ảnh nhưng không biết vật thể nằm ở đâu trong không gian 3D, việc gắp hoặc di chuyển đến vật thể sẽ khó thực hiện. Vì thế, perception trong robotics thường phải quan tâm đồng thời đến semantic, geometry và vị trí.

## Decision making và autonomy

[Slide 10]({{ slide_deck }}#page=10) chia autonomy và decision making thành nhiều lớp:

<figure class="blog-slide">
  <img src="{{ '/assets/images/blog/slides/blog-07-slide-10-robot-learning.jpg' | relative_url }}" alt="Các thành phần của robot learning quanh robotics" loading="lazy">
  <figcaption>Slide 10: robotics là điểm giao giữa perception, decision making, learning, language, vision và human-robot interaction.</figcaption>
</figure>

- **Motion planning:** robot nên di chuyển theo quỹ đạo nào?
- **Task planning:** cần thực hiện chuỗi hành động nào để hoàn thành mục tiêu?
- **Reactive decision making:** phản ứng nhanh với stimulus ngay lập tức.
- **Deliberative decision making:** suy luận có cấu trúc hơn, có thể dùng probabilistic reasoning, Markov Decision Process hoặc Partially Observable MDP.

Một robot tự chủ tốt thường cần cả phản ứng nhanh lẫn khả năng lập kế hoạch dài hạn. Chỉ reactive thì robot dễ hành động thiếu chiến lược; chỉ deliberative thì robot có thể phản hồi quá chậm trước những thay đổi bất ngờ.

## Một số case study về perception và mapping

Phần case study trong seminar cho thấy các thành phần trên có thể xuất hiện trong những công trình cụ thể:

- **PerFc:** một framework phần cứng và phần mềm cho 2D và 3D perception trên mobile cobot.
- **Multiplanar Self-Calibration:** tự calibration cho mobile cobot 3D object manipulation bằng 2D detector và depth estimation.
- **Online 3D Deformable Object Classification:** nhận dạng vật thể biến dạng cho bài toán manipulation.
- **V3D-SLAM:** RGB-D SLAM trong môi trường động với semantic geometry voting.
- **Volumetric Mapping with Panoptic Refinement:** mapping bằng panoptic refinement và kernel density estimation.

[Slide 12-19]({{ slide_deck }}#page=12) lần lượt đi qua các ví dụ về hardware-software framework, self-calibration, multi-view object recognition, SLAM, motion trajectory estimation và 3D reconstruction.

Điểm chung là robot không chỉ cần biết “đây là vật gì”. Nó còn cần biết vật thể nằm ở đâu, môi trường thay đổi ra sao, camera và robot đang ở vị trí nào, rồi dùng thông tin đó để hành động.

## Human-robot interaction và adaptive learning

Robot làm việc với con người cần hiểu nhiều loại tín hiệu hơn một hệ thống vision thông thường. [Slide 22]({{ slide_deck }}#page=22) nhắc đến natural language processing, emotion and sentiment recognition, gesture recognition và khả năng học từ interaction.

Các ứng dụng có thể gồm healthcare assistive robots, service robots và educational robots. Trong những bối cảnh này, accuracy không phải tiêu chí duy nhất. Robot còn cần phản hồi dễ hiểu, an toàn và phù hợp với hành vi của con người.

Adaptive learning là khả năng thích nghi với thay đổi của môi trường. Slide đề cập đến việc học từ unlabeled data thu thập trong quá trình tương tác, cũng như feedback-based learning, trong đó tín hiệu phản hồi hướng dẫn robot học một task mới.

Một số hướng learning được liệt kê gồm:

- **Reinforcement learning:** dùng reward để cải thiện decision making bằng cách khám phá action space.
- **Online learning:** cập nhật model liên tục khi có dữ liệu mới.
- **Transfer learning:** chuyển kiến thức giữa các task tương tự hoặc dùng distillation.
- **Imitation learning:** học bằng cách bắt chước task được demonstrated.
- **Multi-task learning:** học nhiều task đồng thời.

## Simulation, VLM và robot policy

Training robot trực tiếp trong thế giới thật thường tốn kém và có rủi ro. Simulation giúp tạo môi trường an toàn hơn để kiểm tra perception, planning và learning trước khi đưa robot ra ngoài đời.

[Slide 25]({{ slide_deck }}#page=25) giới thiệu ORBIT, một framework simulation cho interactive robot learning environments. Simulation không thay thế hoàn toàn real-world testing, nhưng giúp tăng tốc việc thử nghiệm và tạo dữ liệu ở quy mô lớn hơn.

<figure class="blog-slide">
  <img src="{{ '/assets/images/blog/slides/blog-07-slide-25-octo.jpg' | relative_url }}" alt="Case study Octo về generalist robot policy" loading="lazy">
  <figcaption>Slide 25: Octo minh họa hướng generalist robot policy, nơi nhiều loại dữ liệu và task được dùng để huấn luyện policy.</figcaption>
</figure>

Một hướng khác là **Vision-Language Models**. VLM học quan hệ giữa nội dung hình ảnh và mô tả văn bản, với các task quen thuộc như image captioning và visual question answering. Slide nhắc đến CLIP, GLIP và một survey về VLM cho vision tasks.

Case study về **Octo** cho thấy hướng xây dựng generalist robot policy. Octo là một open-source policy có thể học từ nhiều dataset và nhiều loại task hơn thay vì chỉ được thiết kế cho một robot hoặc một hành động duy nhất.

🔗 [Octo paper](https://arxiv.org/pdf/2405.12213) · [Octo GitHub](https://github.com/octo-models/octo)

Trong pipeline của Octo, dữ liệu đến từ 25 dataset thuộc Open X-Embodiment và có tính heterogeneous. Conditional diffusion decoding dùng thông tin điều kiện để hướng dẫn mạng trong quá trình denoising. Đây là một ví dụ cho thấy robot learning đang kết nối với những ý tưởng lớn từ generative modeling.

## Swarm robotics

Nếu một robot phải tự giải quyết mọi việc, ta đang nói về single-agent autonomy. Với swarm robotics, nhiều robot cùng phối hợp để hoàn thành một mục tiêu.

[Slide 29]({{ slide_deck }}#page=29) đề cập đến coordination, decentralized control và distributed reinforcement learning. Các thuật toán truyền thống được nhắc đến gồm Genetic Algorithm, Finite State Machine, Consensus Algorithm và Particle Swarm Optimization.

Swarm có thể tạo ra khả năng mở rộng và tính linh hoạt, nhưng cũng làm bài toán coordination khó hơn. Mỗi robot chỉ có thông tin cục bộ, nên cả hệ thống cần đạt được hành vi phối hợp mà không nhất thiết có một bộ điều khiển trung tâm.

## Những thách thức còn lại

Slide kết thúc bằng ba nhóm thách thức lớn:

- **Trust:** robot phải đủ chính xác và đáng tin cậy.
- **Ethical decision-making:** robot cần hành động phù hợp khi quyết định ảnh hưởng đến con người.
- **Bias and fairness:** dữ liệu và policy không nên tạo ra hành vi bất công hoặc nguy hiểm cho một nhóm người nào đó.

Đây là những câu hỏi khó vì robot không chỉ tạo ra prediction trên màn hình. Robot có thể di chuyển, chạm vào đồ vật, tương tác với con người và tác động trực tiếp lên môi trường vật lý.

## Kết

AI in Robotics là một không gian rất rộng. Perception giúp robot hiểu môi trường, decision making giúp chọn hành động, learning giúp thích nghi, còn hardware và control biến quyết định thành chuyển động.

Điều mình nhớ nhất từ seminar là câu hỏi mở ở cuối slide: **Nếu robot có thể làm phần lớn công việc của con người, chúng ta sẽ làm gì?**

Có lẽ câu hỏi này không chỉ dành cho tương lai xa. Nó cũng nhắc người làm AI rằng xây dựng một hệ thống thông minh không chỉ là làm model mạnh hơn. Ta còn phải nghĩ về mục tiêu, trách nhiệm, cách con người cộng tác với robot và những giá trị mà hệ thống đó phục vụ.
