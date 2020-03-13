### FaceSwap Examples

* FaceSwap Tutorial 목적의 Repository입니다.

#### 간단한 설명

* Colab 환경에서는 곧잘 실행됩니다.
* FaceSwap 자체가 실제 윈도우/리눅스 환경에서 실행할 때는 다양한 문제가 발생할 수 있으며, 로그(log)가 친절하게 나오지 않습니다.
* Faceswap 라이브러리에서 요구하는 requirements.txt 내용을 모두 설치해야 합니다.
* 필요한 라이브러리를 다 설치하지 않은 경우에는 별도의 로그(log)도 출력되지 않고, FaceSwap이 정지하는 경우도 있습니다.

#### 일반 리눅스 버전 1)

* FaceSwap 설치(Installation)
<pre>
git clone https://github.com/deepfakes/faceswap.git
cd faceswap
pip install -r requirements.txt --user
</pre>

* 학습용 이미지 옮긴 뒤에 특정 폴더에 압축 풀기
<pre>
unzip source_images.zip -d source_images_test_1
unzip target_images.zip -d target_images_test_1
rm source_images.zip
rm target_images.zip
</pre>

* 학습 진행하기
<pre>
python3 faceswap.py train -A "source_images_test_1" -B "target_images_test_1" -m "./test_1" -t "villain" -bs 16 -s 330 -w -nl
</pre>

* 학습을 처음 진행하는 경우 설정파일이 .faceswap에 저장됩니다.

* Slurm Batch 예제
* test_1.sh
<pre>
#!/bin/sh

#SBATCH -J faceswap_test_1
#SBATCH -o faceswap_test_1.%j.out
#SBATCH -p gpu-titanxp
#SBATCH -t 264:00:00

#SBATCH --gres=gpu:1
#SBATCH --ntasks=1
#SBATCH --tasks-per-node=1
#SBATCH --cpus-per-task=1

cd  $SLURM_SUBMIT_DIR

echo "SLURM_SUBMIT_DIR=$SLURM_SUBMIT_DIR"
echo "CUDA_HOME=$CUDA_HOME"
echo "CUDA_VISIBLE_DEVICES=$CUDA_VISIBLE_DEVICES"
echo "CUDA_VERSION=$CUDA_VERSION"

srun -l /bin/hostname
srun -l /bin/pwd
srun -l /bin/date

python3 faceswap.py train -A "source_images_test_1" -B "target_images_test_1" -m "./test_1" -t "villain" -bs 16 -s 330 -w -nl

date

squeue  --job  $SLURM_JOBID

echo  "##### END #####"
</pre>

* 이후에 sbatch test_1.sh로 실행
