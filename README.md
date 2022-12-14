
# Object Detection Project

## ๐ย **ํ๋ก์ ํธ ๊ธฐ๊ฐ**

22.08.19 ~ 22.08.31

## ๐ย **ํ๋ก์ ํธ ๋ชฉ์ **

์์ธ์ ๋ธํ ๊ฑด์ถ๋ฌผ์ ์ฆ๊ฐ๊ฐ ์์๋์ด ์ด๋ฅผ ๊ฐ์ฒด์ธ์์ ์ด์ฉํด ์ด์ ํ์ง ์๋ํ

## ๐ช **์ญํ **

<table>
    <thead>
        <tr>
            <th>์ด๋ฆ</th>
            <th>์ญํ </th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>๊ตญ์น์ฉ</td>
            <td>PPT</td>
        </tr>
         <tr>
            <td>๊ฐํจ์ง</td>
            <td>PPT</td>
        </tr>
        <tr>
            <td>๊น๊ท์ธ</td>
            <td>PPT</td>
        </tr>
        <tr>
            <td>์ฌ๋ฏผํฌ</td>
            <td>PPT</td>
        </tr>
        <tr>
            <td>์ด์ฌํ</td>
            <td>PPT</td>
        </tr>
    </tbody>
</table>


## ๐๏ธย **๋ฐ์ดํฐ์**

- AI Hub - ์์ธ์ ๋ธํ ์ฃผํ ๊ท ์ด ๋ฐ์ดํฐ

- 5๊ฐ์ ์นดํ๊ณ ๋ฆฌ์ ๋ฐ์ดํฐ 20,663๊ฐ ์ฌ์ฉ

    | ์นดํ๊ณ ๋ฆฌ | ๊ท ์ด (Crack) | ๋ฐ๋ฆฌ/๋ฐ๋ฝ (Peel-off) | ์ฒ ๊ทผ๋ธ์ถ (Rebar-Exposure) | ๋์ง (Block) | ๋ง๊ฐ (Finish) |
    | --- | --- | --- | --- | --- | --- |
    | ๊ณ | 4384 | 4596 | 4414 | 3559 | 3710 |


## โ๏ธย **Preprocess**

- resize & padding

    - 1080 * 1440 โ 416 * 416
    
    - ์ฌ์ง์ ํฌ๊ธฐ๋ ์ปธ๊ณ , ์ผ๋ฐ์ ์ธ ํ์ต์ฌ์ด์ฆ์ธ 416 * 416์ผ๋ก resize
    
- polygon to bounding box

    - ๋ผ๋ฒจ ๋ฐ์ดํฐ๊ฐ polygon ํํ๋ก ๋์ด ์์ด์(xyxy ํํ) ์ด๋ฅผ bounding boxํํ๋ก ๋ฐ๊ฟ์ค(xywhํํ)
    
   ![image](https://user-images.githubusercontent.com/73925429/203080560-002f164a-affd-4b7d-99c2-7c008232c6cf.png)


## ๐ย **Modeling**

- Baseline - YOLOv4
    | Params | max_batch | # of labels | subdivision | IoU_threshold | IoU_loss | mAP |
    | --- | --- | --- | --- | --- | --- | --- |
    | ๊ณ | 10000 | 5 | 16 | 0.213 | CIoU | 77.46% |
    

- subdivision(ํ๋ฒ์ ๋ฐ์ดํฐ๋ฅผ ์ฒ๋ฆฌํ๋ ์) : 16 โ 32 ๋ก ์ฆ๊ฐ

      weight์ ๋ณํ๊ฐ ์ ์ด ์ฑ๋ฅ์ด ๋จ์ด์ง ๊ฒ์ด๋ผ ์์ธก
      
      mAP 77.46 โ 74.03% ํ๋ฝ

- IoU_loss : CIoU โ DIoU, GIoU

      ํฐ ์ฐจ์ด X

- max_batch 10,000 : mAP 77.46%

      10,000 โ 5,000 (๊ฐ์) : underfit  mAP 68.06%
    
      10,000 โ 15,000 (์ฆ๊ฐ) : ์ฑ๋ฅ ๊ฐ์  mAP 82.03%

- IoU threshold(์์ธก๊ฐ๊ณผ ์ค์ ๊ฐ์ด ๊ฒน์น๋ ์ ๋์ ๊ธฐ์ค)

      max_batch 10,000 โ 15,000 + IoU threshold(0.213) : mAP 82.03%
      
      max_batch 10,000 โ 15,000 + IoU threshold(0.750) : mAP 79.51%
    
      max_batch 10,000 โ 15,000 + IoU threshold(0.950) : mAP 70.61%

- IoU normalizer(0.07 โ 0.5) + learning rate ์ฆ๊ฐ

      learning rate์ ํญ์ ํฌ๊ฒ ์ก์๋๋ ๊ฒฐ๊ณผ ๊ฐ์ด ์ข์ง ์์ ์์ 
      
    ![image](https://user-images.githubusercontent.com/73925429/203082171-423c9693-8474-46a9-a505-1e59e170cb5a.png)

      3๋ฒ์งธ ๊ฒฝ์ฐ๊ฐ underfit์ด๋ผ๊ณ  ํ๋จํด์ max_batch๋ฅผ 15,000์ผ๋ก ์ฆ๊ฐ
      
      mAP 78.36% โ mAP 81.78% ์ฆ๊ฐ
      
- max_batch 10,000 โ 15,000 + ignore_threshold(0.7 โ 0.8)

      mAP 77.46%(Baseline)  โ mAP 83.41%๋ก ์ฆ๊ฐ
      
- ์ก์์ผ๋ก ๊ตฌ๋ถํ๊ธฐ ํ๋  ๋ผ๋ฒจ์ ์ ๊ฑฐํ๋ฉด, ๋๋จธ์ง ๋ผ๋ฒจ์ ํท๊ฐ๋ฆฌ์ง ์๊ณ  ์ ์์ธกํ ๊ฑฐ๋ผ ์๊ฐ

      block ๋ผ๋ฒจ ์ ๊ฑฐ โ class = 4๊ฐ
      
    <img width="911" alt="image" src="https://user-images.githubusercontent.com/73925429/203097133-fb0c5e58-5d06-4db1-aff4-6bb9123e19b8.png">

      ์ฑ๋ฅ ํ๋ฝ

## ๐ย **์ํ ๊ฒฐ๊ณผ**

- Best Params

  <img width="850" alt="image" src="https://user-images.githubusercontent.com/114709620/203184699-d165aef7-29c3-4a0e-9940-f9501d5d14de.png">
  
  
-  ํ๊ฐ์งํ 
    - mAP(mean Average Precision) : ์ฌ๋ฌ object detector์ Average Precision์ ๋ฉด์ ์ผ๋ก ํ๊ฐํ๋ ๋ฐฉ์. AP๋ ์ธ์๊ธฐ๊ฐ ๊ฒ์ถํ ์ ๋ณด ์ค์์ ground truth์ ์ผ์นํ๋ ๋น์จ๋ค์ ํ๊ท .
    - IoU(Intersection over Union) : ์ค์  ๊ฐ์ฒด ์์น ground truth์ ์์ธก๊ฐ prediction ๋ box๊ฐ ์ค๋ณต๋๋ ์์ญ์ ํฌ๊ธฐ๋ฅผ ํตํด ํ๊ฐํ๋ ๋ฐฉ์์ผ๋ก ๊ฒน์น๋ ์์ญ์ด ๋์์๋ก ์ ์์ธกํ ๊ฒ์ผ๋ก ํ๊ฐ 
  
- confidence

  ![image](https://user-images.githubusercontent.com/73925429/203095108-0aead6ed-acc2-4d1c-8e8e-9ef192993481.png)


## ๐ผ๏ธ **์ค์ํ ์ ์ฉ**

- ์ฑ์๋

  ![image](https://user-images.githubusercontent.com/73925429/203095307-4cebec18-2ffe-409e-acf8-949dfae194f5.png)

- ์ํ์ฝ

  ![image](https://user-images.githubusercontent.com/73925429/203095383-1507e609-7702-42e0-99a9-23e0e1884121.png)


## ๐๐ปย **๋ณด์์ **

   ~์ถํ ์ถ๊ฐ ์์ ~

## Reference

[1] [YOLOv4: Optimal Speed and Accuracy of Object Detection](https://arxiv.org/pdf/2004.10934.pdf): Bochkovskiy, Alexey, Chien-Yao Wang, and Hong-Yuan Mark Liao. "Yolov4: Optimal speed and accuracy of object detection." arXiv preprint arXiv:2004.10934 (2020).
