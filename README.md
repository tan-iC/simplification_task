# 単言語多段階平易化

- [fairseq](https://github.com/fairseq)における単言語多段階平易化タスクを作成
- 難易度を表す特殊トークンを入力に付与することで実現される系列変換モデル

## リポジトリ内容

- ```task```: 単言語多段階平易化のタスク。```fairseq/fairseq/tasks```に追加するファイル
- ```data```: 単言語多段階平易化のタスクで利用するディクショナリ。```fairseq/fairseq/data```に追加するファイル
- ```README.md```: このファイル

## 用法

- fairseqをインストールし、上記ファイルを所定のディレクトリに追加。

## 用例

- アーキテクチャとしてTransformerを用いた場合

```bash
fairseq-preprocess \
       --source-lang {.comp} --target-lang {.simp} \
       --task simplification \
       --trainpref {traindir} \
       --validpref {devdir} \
       --testpref {testdir} \
       --destdir {destdir} \
       --workers 8
```

```bash
CUDA_VISIBLE_DEVICES=0 fairseq-train \
       {destdir}/ \
       --source-lang {.comp} --target-lang {.simp} \
       --task simplification \
       --arch transformer --share-decoder-input-output-embed --activation-fn relu \
       --optimizer adam --adam-betas '(0.9, 0.98)' --clip-norm 0.0 \
       --lr 7e-4 --lr-scheduler inverse_sqrt --warmup-updates 4000 --warmup-init-lr 1e-7 \
       --weight-decay 0.0001 \
       --criterion label_smoothed_cross_entropy --label-smoothing 0.1 \
       --max-tokens 80000 --patience 10 \
       --save-dir checkpoints/{model_name} \
       --max-epoch 200 --no-epoch-checkpoints \
       --fp16
```

```bash
fairseq-generate \
       {destdir}/ \
       --source-lang {.comp} --target-lang {.simp} \
       --task simplification \
       --gen-subset test \
       --path checkpoints/{model_name}/checkpoint_best.pt \
       --max-len-a 1 --max-len-b 50 \
       --beam 5 --lenpen 1.0 \
       --nbest 1 \
       --results-path results/{model_name} \
       --fp16
```
