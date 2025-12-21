- **val/testのパッチ切り出しを random じゃなくする**（Center crop or 固定seed）
    
    → 評価がブレにくくなる
    
- num_workers=0 は遅いので、環境が許せば増やす
    
- 画像が病理系なら Normalize を ImageNet固定にせず、データに合わせた平均分散を計算するのも効くことがある

次にやるとしたらおすすめは：

1. **num_patches = 4 vs 8 の比較**
    
2. **Attention重みの可視化（どこを見てるか）**
    
3. **Random crop を固定にした validation 比較**