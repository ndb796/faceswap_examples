### FaceSwap Examples

* FaceSwap Tutorial 목적의 Repository입니다.

#### 간단한 설명

* Colab 환경에서는 곧잘 실행됩니다.
* FaceSwap 자체가 실제 윈도우/리눅스 환경에서 실행할 때는 다양한 문제가 발생할 수 있으며, 로그(log)가 친절하게 나오지 않습니다.
* Faceswap 라이브러리에서 요구하는 requirements.txt 내용을 모두 설치해야 합니다.
* 필요한 라이브러리를 다 설치하지 않은 경우에는 별도의 로그(log)도 출력되지 않고, FaceSwap이 정지하는 경우도 있습니다.

#### 기타

* 클러스터 노드에서 다음과 같은 오류가 발생하는 경우
<pre>
  File "/home/dongbinna/DeepFakes_Research/faceswap/lib/cli.py", line 128, in execute_script
    process.process()
  File "/home/dongbinna/DeepFakes_Research/faceswap/scripts/train.py", line 157, in process
    err = self._monitor(thread)
  File "/home/dongbinna/DeepFakes_Research/faceswap/scripts/train.py", line 343, in _monitor
    keypress = KBHit(is_gui=self._args.redirect_gui)
  File "/home/dongbinna/DeepFakes_Research/faceswap/lib/keypress.py", line 42, in __init__
    self.new_term = termios.tcgetattr(self.file_desc)
termios.error: (25, 'Inappropriate ioctl for device')
</pre>
* 일반적으로 GUI 자체가 없는 컴퓨터에서 발생할 수 있는 오류입니다.
* scripts/train.py 파일에서 다음의 부분 앞에 '#'을 붙여서 주석 처리
<pre>
keypress = KBHit(is_gui=self._args.redirect_gui)
</pre>
* 이후에 바로 아래쪽의 'Console Monitor' 부분 전체 주석 처리

#### 일반 리눅스 버전 1) 한 폴더 안에서 프로젝트 나누기

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

* Slurm Batch 예제 (test_1.sh)
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

#### 일반 리눅스 버전 2) 프로젝트마다 클론(Clone)

* FaceSwap 설치(Installation)
<pre>
git clone https://github.com/deepfakes/faceswap.git
mv faceswap faceswap_test_1
cd faceswap faceswap_test_1
pip install -r requirements.txt --user
</pre>

* 학습용 이미지 옮긴 뒤에 특정 폴더에 압축 풀기
<pre>
unzip source_images.zip -d source_images
unzip target_images.zip -d target_images
rm source_images.zip
rm target_images.zip
</pre>

* 학습 진행하기
<pre>
python3 faceswap.py train -A "source_images" -B "target_images" -m "./trained_models" -t "villain" -bs 16 -s 330 -w -nl
</pre>

* 학습을 처음 진행하는 경우 설정파일이 .faceswap에 저장됩니다.

* Slurm Batch 예제 (test_1.sh)
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

python3 faceswap.py train -A "source_images" -B "target_images" -m "./trained_models" -t "villain" -bs 16 -s 330 -w -nl

date

squeue  --job  $SLURM_JOBID

echo  "##### END #####"
</pre>

* 이후에 sbatch test_1.sh로 실행
