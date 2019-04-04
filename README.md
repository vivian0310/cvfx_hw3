# Homework3-GAN-Dissection
* Original method: GANDissect
* Other method: Inpainting

## Generate with GANPaint

- 下方gif為GANPaint的測試過程 (約15秒，上面網址為錄影程式浮水印)
![](https://i.imgur.com/j5xM6aj.gif)

過程中，我們一開始先畫出草地與樹木，草地部分可能是因為畫在接近地面的部分，所以銜接的十分自然；樹木的部分若畫在建築物兩側空白處，則結果較為完整，但如果畫樹木的筆接觸到建物就會生出樹葉在建築上，看起來較為不規則，而且建築物顏色也起了變化，從磚紅色轉變為褐色。然而在移除雲朵的部分就看起來較沒那麼成功，移除之後反而多了很多白色的部分，更像在天空增添很厚的雲層。最後我們測試去清除先前畫的草地部分，也有順利地被移除掉。

- 下方為對不同圖片進行新增與移除物件後的結果
![](https://imgur.com/r5njfWA.jpg)
![](https://imgur.com/ZBPIRIv.jpg)

根據這些結果可以發現，若是新增或移除的物件有符合到圖片中應存在的位置，則圖畫後的結果就十分自然，跟周圍景物的契合度也很高，然而在移除門的部分其結果看起來就比較不成功。

## Dissect GAN model  (LSUN living room progressive GAN)
GAN Dissection的主要目的是讓人了解GAN model的每層結果分析中，神經元(unit)學習到什麼樣子的物件或類別，並使人方便去對應各種物件與神經元間的關係。利用此方式能找出哪些學習偏差的神經元導致影響輸出的效果，而且透過不同的dataset，其神經元也會學到不同的特徵。

這次我們dissect的model為範例提供的LSUN living room progressive GAN，觀察的層為layer1、layer4、layer7。透過web server開啟livingroom/dissect.html之後會得到各層的網路剖析結果，如下圖：

![](https://i.imgur.com/TocPM9w.jpg)

web最上方的圖表為Unit class distribution，能夠知道不同layer中每個類別所使用的unit數量。下圖由上至下分別為layer1、4、7各層的unit圖表：

![](https://i.imgur.com/rXfVYIV.png)

### Analysis
根據呈現出來的結果及圖表，發現layer1的unit僅學到天花板一類，其餘都沒有學習到，而後面的layer學習成果就進步許多，透過layer4與layer7的圖表比較可以發現layer4所學到的類別多於layer7，但在材質的部分layer7比layer4多了木頭的類別，由於沒有layer7之後的結果，只能大致推測越往後的layer可能較注重在質地等細節。雖然有些圖片中unit圈出的物件區域沒有那麼精準，但unit分辨不同圖片中的同類別物件效果蠻不錯的，像是不同顏色的植物以及不同樣式的沙發等等，大部分都有被辨認出來。

## Compare with other method: [Inpainting](https://github.com/akmtn/pytorch-siggraph2017-inpainting)
- 架構
![](https://imgur.com/qEPwO3q.jpg)
此方法的架構主要由三個network所構成，分別為completion network、global context discriminator和local context discriminator。completion network為用於完成圖片的convolutional network；而global context discriminator用於判別整張圖片是否有完整的連貫性；local context discriminator則更仔細地針對mask以外的區域判別是否確實完成連貫。

- 介紹  
藉由fully-convolutional neural network，此方法主要可以利用任意形狀的mask將原照片中的部分區塊移除，並生成可連續性填補遺失區域的圖片。除此之外，其可用於各式各樣的場景，甚至可用於臉部等的高度特定結構。

## Generate with Inpainting
![](https://imgur.com/XAkrTyb.jpg)

### Analysis
由以上的生成結果可以發現，若是mask中即將移除的白色區塊過大，將導致生成的圖片不盡理想。Inpainting常常只有把要去除的部分弄模糊，而無法將要消除的物件完全去除，且有時會出現完全不符合背景色系的物體。總而言之，Inpainting的生成結果時而較成功，時而卻失敗，但即便參考所有input的圖片，仍不易歸納出影響成敗的原因。


## Overall Comparison
![](https://imgur.com/Nb1FhK6.jpg)

### Analysis
- 以整體結果而言，GANPaint的移除效果比Inpainting還來的好，而且移除的位置與四周的景物也有相互補足，整張圖片看起來就較為自然，而Inpainting的修改結果就沒那麼理想，容易出現與周遭景物不符的顏色或圖形，而且通常是利用有點略為透明(類似翅膀)的材質去覆蓋，導致整張圖修改之後反而更像多了一塊物體。
- 對修改的範圍而言，GANPaint對於大範圍或小範圍的圖形增減效果都不錯。但使用Inpainting時，去除大範圍的物體時幾乎都會失敗，即使在周遭的背景很單一的狀況下，也很難將周遭的背景成功補上被去除的部分；而若是去除小範圍的物體時，雖然也有生成失敗的例子，但相對去除大範圍物體而言，生成成功的機率較高。
- 在取代物件的部分，GANpaint用以取代被移除的物件就比較制式化的感覺，例如以石地取代草地、以窗戶取代門等等，而Inpainting就沒有利用特定物件來進行填充，雖然以目前執行的結果看起來其填補結果不盡理想，但相較GANPaint而言就較為即興變化。




