
## DETRs Beat YOLOs on Real-time Object Detection

### Abstract

YOLO achieves reasonable accuracy-speed trade-off.

YOLO’s speed and accuracy negatively impacted by NMS.

Recent DETR without NMS has high computational cost.

This paper suggests RT-DETR (real-time).

### 1. Introduction

### 2. Related Work

### 3. End-to-end Speed of Detectors

### 4. The Real-time DETR

### 5. Experiments

- 5.1
    
    input size
    
    - RT-DETR, YOLO : (640, 640)
    - 다른 DETR : (800, 1333)
    
    RT-DETR-R50 : **53.1% AP**와 **108 FPS**
    
    RT-DETR-R101 : **54.3% AP**와 **74 FPS**
    
    ⇒ 속도, 정확성 모두 YOLO, DETR보다 나음
    
    Real-time Detectors와 비교
    
    - **YOLOv5-L / PP-YOLOE-L / YOLOv6-L**과 비교:
        
        RT-DETR-R50은 **4.1% / 1.7% / 0.3% AP**의 정확도 향상, **100.0% / 14.9% / 9.1%**의 FPS 증가, **8.7% / 19.2% / 28.8%**의 파라미터 감소를 달성했습니다.
        
    - **YOLOv5-X / PP-YOLOE-X**와 비교:
        
        RT-DETR-R101은 **3.6% / 2.0% AP**의 정확도 향상, **72.1% / 23.3%**의 FPS 증가, **11.6% / 22.4%**의 파라미터 감소를 달성했습니다.
        
    - **YOLOv7-L / YOLOv8-L**과 비교:
        
        RT-DETR-R50은 **1.9% / 0.2% AP**의 정확도 향상, **96.4% / 52.1%**의 FPS 증가를 보였습니다.
        
    - **YOLOv7-X / YOLOv8-X**와 비교:
        
        RT-DETR-R101은 **1.4% / 0.4% AP**의 정확도 향상, **64.4% / 48.0%**의 FPS 증가를 보였습니다.
        
    
    End-to-End Detectors와 비교
    
    - **DINO-Deformable-DETR-R50**과 비교:
        
        RT-DETR-R50은 정확도를 **2.2% AP** 향상시키고, 속도는 **21배 (108 FPS vs 5 FPS)** 개선
        
- 5.2
    
    **A to E 까지 ablation study로 한 가지씩 변경해가며 Average Precision, # of parameters, Latency를 비교함**
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/c09f8228-29c7-4dcb-8ca3-1de7d3988fab/198a454f-0877-43ec-ad76-d6d0b8de7340/image.png)
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/c09f8228-29c7-4dcb-8ca3-1de7d3988fab/4f4a828e-0986-4b1b-8717-84a546af1ca0/image.png)
    
    1. A → B:
        
        1. Intra-scale feature interaction is significant, but single-scale Transformer encoder is computationally expensive.
    2. B → C:
        
        1. Cross-scale feature fusion is significant but multi-scale Transformer encoder is computationally expensive.
    3. C → D:
        
        1. Separating intra-scale interaction and cross-scale fusion reduces computational cost and improves accuracy.
    4. D → Ds5:
        
        1. Intra-scale interactions of lower-level features are not required. 백본에서 S5 스테이지만 고려.
    5. Ds5 → E:
        
        1. Use Attention based Intra-scale Feature Interaction and CNN-based Cross-scale Feature Fusion.
- 5.3
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/c09f8228-29c7-4dcb-8ca3-1de7d3988fab/bb2a1a0a-fb62-4102-8300-71e7542263bf/image.png)
    
    300개의 인코더 피쳐를 분류 스코어에 따라 추출
    
    PROP_cls: 분류 스코어 0.5 이상
    
    PROP_both: 분류 스코어와 IoU 둘다 0.5 이상
    
    **결론: Uncertainty-minimal 메소드가 분류 스코어를 높이는 인코더 피쳐를 추출할 수 있었음 + 조금 더 좋은 퀄리티의 피쳐를 추출했음**
    
- 5.4
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/c09f8228-29c7-4dcb-8ca3-1de7d3988fab/a1eeab58-dffc-4181-8591-486b6ab6af28/image.png)
    
    <aside> 💡
    
    ### **[본문 내용]**
    
    - 디코더 레이어 수에 따른 추론 지연 시간 (Latency)와 정확도(Accuracy)
    - 레이어 증가 → 인접한 디코더 레이어 간 정확도 차이 감소
    - 위 결과를 기반으로 **디코더 레이어 수**를 조정해서 재학습 없이도 **속도**를 조정할 수 있음을 보임. </aside>
    
    ---
    
    ### 해석
    
    - 디코더 레이어 수가 많아질수록, 정확도 (AP) 개선 기여도는 점차 감소함
    - 즉, 마지막 몇 개의 디코더 레이어는 정확도를 크게 향상시키지는 못하지만, 속도는 더 느려지게 함.
    - 유연하게 조정해야하고 재학습이 필요하지 않다고 주장함.
    
    <aside> ❓
    
    ### 재학습이 필요하지 않다는게 무슨 말일까?
    
    - 일반적으로 레이어를 변경하면 재학습이 필요하다고 합니다. 그 이유는 추가되거나 제거된 레이어에는 학습된 **가중치**가 없어 새로운 가중치를 학습해야하기 때문이라고 합니다.
    - 하지만 RT-DETR에서는 디코더 레이어 일부를 제거하거나 추가해도, 각 레이어가 이전 레이어의 출력을 반복적으로 최적화 하는 구조여서 나머지 부분이 이미 학습된 가중치를 유지한다고 합니다. (나머지 부분: 백본, 인코더, 기존 디코더 레이어) </aside>

### 6. Discussion & 7. Conclusion

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/c09f8228-29c7-4dcb-8ca3-1de7d3988fab/2d1bae49-d7dc-4ff8-b84a-905266368798/image.png)

<aside> 💡

### **[본문내용]**

### Limitation

- **작은 객체(Small object)**에 대한 성능이 다른 실시간 탐지기보다 열등함.
- 위 Table 2에 따르면, RT-DETR-R50은 YOLOv8-L 모델의 최고 AP_S 보다 0.5% AP 낮음
- 또, RT-DETR-R101은 YOLOv7-x 모델의 최고 AP_S보다 0.9% AP 낮음

---

### Discussion

- RT-DETR은 설계적으로 **DETR 계열의 디코더 구조와 유사성을 유지**해서 기존의 DETR 계열 모델의 사전 학습된 가중치를 쉽게 전이할 수 있음.
- 대규모 DETR모델에 **사전 학습된 가중치를 RT-DETR로 전이**하여 높은 정확도를 올릴 수 있을 것으로 예상함. (Distillation; 대규모 모델의 정보를 경량 모델로 쉽게 전달) </aside>