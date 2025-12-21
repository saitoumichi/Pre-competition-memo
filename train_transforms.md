train_transforms = transforms.Compose([

 #1. リサイズとクロッピング（モデルの入力サイズに合わせる）

 transforms.Resize((256, 256)), # まず少し大きくリサイズ

 transforms.RandomCrop((224, 224)), # ランダムに224x224を切り出す (変更点: Scale Augmentationに近い効果)

 #2. 幾何学的な変換 (Geometric Transforms)

 transforms.RandomHorizontalFlip(), # 水平反転 (既存)

 transforms.RandomRotation(degrees=30), # ランダムな回転（±15度以内） (追加)

 transforms.RandomAffine(

 degrees=0,

 translate=(0.1, 0.1), # 画像の最大10%分をランダムに平行移動 (追加)

 scale=(0.9, 1.1), # 0.9倍から1.1倍にランダムに拡大縮小 (追加)

 shear=5 # シアー（せん断）変換（±5度以内） (追加)

 ),

  ## **1)** **RandomCrop(224,224)****が “当たり外れ” を作りやすい**
   Resize(256) → RandomCrop(224) は、切り出し位置によっては **重要部位が端にあった
   場合に切れて消える** ことがある。
    ###  **代替案A（安定重視）**
     - 検証と同じく中心に寄せる
     - `transforms.Resize((256, 256)),`
      `transforms.CenterCrop((224, 224)),`
      → 精度が上がりやすい（ただし汎化はやや弱め）
      ### **代替案B（汎化しつつ壊しにくい）**
      RandomResizedCrop を “軽め” に
 .       
```
      transforms.RandomResizedCrop(224, scale=(0.85, 1.0), ratio=(0.95, 1.05))
```
    
      → 病変を消しにくく、スケール揺れも入る
    **2)** **RandomRotation(degrees=30)は強め（病理/医療は特に）**
    ±30°は、模様・境界・形状を大きく変えてしまうことがある。
    コメントに「±15度以内」ってあるけど、コードは30になってる（ズレてる）ので、まずここは揃えたい。
    おすすめは **±10〜15°** くらいから。
    transforms.RandomRotation(degrees=15)
    ## **3)** **RandomAffineの translate / shear が “壊し” になりやすい**
    - translate=(0.1,0.1)：結構動く（最大10%）
    - shear=5：テクスチャを歪める
    - 医療画像は「歪み」がラベルと無関係なことが多いので、ここは弱めるのが無難。
    - 例：
```
    - transforms.RandomAffine(
    degrees=0,
    translate=(0.05, 0.05),
    scale=(0.95, 1.05),
    shear=0
    )
```
    まずのおすすめ構成（実務で当たりやすい”弱め”版）
```
    train_transforms = transforms.Compose([
    transforms.RandomResizedCrop(224, scale=(0.85, 1.0), ratio=(0.95, 1.05)),
    transforms.RandomHorizontalFlip(p=0.5),
    transforms.RandomRotation(15),
    transforms.RandomAffine(degrees=0, translate=(0.05,0.05), scale=(0.95,1.05), shear=0),
    transforms.ToTensor(),
    transforms.Normalize(mean=[0.485,0.456,0.406], std=[0.229,0.224,0.225]),
    ])
```


   
# 3. 色調変換 (Color Transforms)

transforms.RandAugment(),

transforms.ColorJitter(

brightness=0.1, # 明るさの変動 (追加)

contrast=0.1, # コントラストの変動 (追加)

saturation=0.1, # 彩度の変動 (追加)

hue=0.0 # 色相の変動 (追加) #0.0のとき77%

),