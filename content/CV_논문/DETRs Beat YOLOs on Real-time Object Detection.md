
### Abstract
 YOLO 시리즈는 속도와 정확도 간의 합리적인 균형으로 실시간 객체 탐지에서 가장 인기 있는 프레임워크가 되었지만, NMS(Non-Maximum Suppression)로 인해 성능이 제한된다. 최근 NMS를 제거한 end-to-end Transformer 기반 탐지기(DETRs)가 대안으로 제시되었으나, 높은 계산 비용으로 실용성이 제한되었다. 본 논문에서는 이러한 딜레마를 해결하는 최초의 실시간 end-to-end 객체 탐지기인 Real-Time DEtection TRansformer (RT-DETR)를 제안한다. RT-DETR은 두 단계로 구축되었다: 먼저 속도를 개선하면서 정확도를 유지하고, 그 다음 속도를 유지하면서 정확도를 개선한다. 구체적으로, 효율적인 hybrid encoder를 설계하여 intra-scale 상호작용과 cross-scale 융합을 분리함으로써 속도를 개선했다. 그리고 uncertainty-minimal query selection을 제안하여 decoder에 고품질의 초기 쿼리를 제공함으로써 정확도를 향상시켰다. 또한, RT-DETR은 decoder 레이어 수를 조정하여 재훈련 없이 다양한 시나리오에 적응할 수 있는 유연한 속도 조정을 지원한다. RT-DETR-R50/R101은 COCO 데이터셋에서 53.1%/54.3% AP를 달성하며 T4 GPU에서 108/74 FPS의 속도를 보여, 기존의 YOLO 모델들을 속도와 정확도 모두에서 능가한다. 더욱이, RT-DETR-R50은 DINO-R50보다 정확도에서 2.2% AP, 속도에서 약 21배 향상된 성능을 보인다. Objects365로 사전 훈련 후 RT-DETR-R50/R101은 55.3%/56.2% AP까지 성능이 향상되었다.




## 4. The Real-time DETR
 

### 4.1. Model Overview
![[Pasted image 20241202111722.png]]

  RT-DETR은 backbone, 효율적인 encoder 그리고 보조 예측기가 있는 transformer decoder로 구성되어 있다.
  - backbone의 마지막 3단계에서 추출된 feature {S3, S4, S5}들을  encoder에 입력 
  - Efficient  hybrid-encoder는 intra-scale feature interaction과 cross-scale feature을 fusion을 통해 multi-scale feature들을 image sequence로 변환한다. (sec. 4.2 ) 
   - uncertainty-minimal query selection을 사용하여 encoder feature들 중 일정 수를 선택하여 decoder의 initial object query로 사용한다. (sec. 4.3)
   - 보조 예측 헤드가 있는 decoder가 object query를 반복적으로 optimize하여 최적화하여 object의 categori와 box를 생성한다. 


### 4.2 Efficient Hybrid Encoder
![[Pasted image 20241202104940.png]]
- Computational bottleneck analysis
	- sequence length가 길어지면 bottleneck이 발생하는 문제점이 생김

	- 고수준 특징(high-level features)은 이미 저수준 특징(low-level features)으로부터 추출된 정보를 포함하고 있습니다.

	- 따라서 여러 스케일의 특징들을 단순히 연결해서 처리하는 것은 계산적으로 중복된 작업입니다.

	- 이러한 가설을 검증하기 위해 연구팀은 다양한 형태의 인코더 구조를 실험했습니다.



### 4.3  Uncertainty-minimal Query Selection






## Q & A 
	Q1. object가 겹쳐 있을때 anchor box가 없어지는 문제점을 해결하기 위해 NMS가 도입되었는데 이 NMS 자체가 모델에 왜 부정적인 영향을 끼치는가? 
	AQ1. NMs을 사용한다것 자체가 confidence, iou threhold값을 통해 model의 정확성을 높이는것인데 threhold값이 huristic한 방법으로 hyperparamter를 통해 조절해야 하고 실시간 object인 경우 계속 다른 threhold값을 줘야함으로 inference speed에 부정적인 영향을 미친다. 
	또한 threhold값을 통해 정확도가 높다고 해서 model의 inference가 좋다고 판단하기에는 어렵지 않나? 




