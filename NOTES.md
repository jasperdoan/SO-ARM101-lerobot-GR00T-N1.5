sudo chmod 666 /dev/ttyACM0
sudo chmod 666 /dev/ttyACM1


=====================================================================


/dev/ttyACM1    --> Leader          --> 5V
lerobot-setup-motors \
    --teleop.type=so101_leader \
    --teleop.port=/dev/ttyACM1

lerobot-calibrate \
    --teleop.type=so101_leader \
    --teleop.port=/dev/ttyACM1 \
    --teleop.id=leader_arm

-------------------------------------------
NAME            |    MIN |    POS |    MAX
shoulder_pan    |    754 |   2019 |   3230
shoulder_lift   |    871 |    885 |   3271
elbow_flex      |    862 |   3054 |   3054
wrist_flex      |    829 |    835 |   3169
gripper         |   1903 |   1922 |   3135

Calibration saved to /home/jasper/.cache/huggingface/lerobot/calibration/teleoperators/so_leader/leader_arm.json


====================================================================


/dev/ttyACM0    --> Follower        --> 12V
lerobot-setup-motors \
    --robot.type=so101_follower \
    --robot.port=/dev/ttyACM0

lerobot-calibrate \
    --robot.type=so101_follower \
    --robot.port=/dev/ttyACM0 \
    --robot.id=follower_arm

-------------------------------------------
NAME            |    MIN |    POS |    MAX
shoulder_pan    |    884 |   2044 |   3320
shoulder_lift   |    756 |    808 |   3353
elbow_flex      |    868 |   3055 |   3061
wrist_flex      |    805 |   1985 |   3153
gripper         |   1899 |   1911 |   3363

Calibration saved to /home/jasper/.cache/huggingface/lerobot/calibration/robots/so_follower/follower_arm.json


====================================================================

lerobot-teleoperate \
    --robot.type=so101_follower \
    --robot.port=/dev/ttyACM1 \
    --robot.id=follower_arm \
    --robot.cameras="{ front: {type: opencv, index_or_path: 0, width: 640, height: 480, fps: 30, fourcc: "MJPG"}, wrist: {type: opencv, index_or_path: 2, width: 640, height: 480, fps: 30, fourcc: "MJPG"}}" \
    --teleop.type=so101_leader \
    --teleop.port=/dev/ttyACM0 \
    --teleop.id=leader_arm \
    --display_data=true

====================================================================

Record:
lerobot-record \
    --robot.type=so101_follower \
    --robot.port=/dev/ttyACM1 \
    --robot.id=follower_arm \
    --robot.cameras="{ front: {type: opencv, index_or_path: 0, width: 640, height: 480, fps: 30, fourcc: "MJPG"}, wrist: {type: opencv, index_or_path: 2, width: 640, height: 480, fps: 30, fourcc: "MJPG"}}" \
    --teleop.type=so101_leader \
    --teleop.port=/dev/ttyACM0 \
    --teleop.id=leader_arm \
    --display_data=true \
    --dataset.repo_id=so101/clean_table \
    --dataset.num_episodes=10 \
    --dataset.single_task="Pick up and move the cup to the marker" \
    --dataset.push_to_hub=false \
    --dataset.episode_time_s=30 \
    --dataset.reset_time_s=5


Resume:
lerobot-record \
    --robot.type=so101_follower \
    --robot.port=/dev/ttyACM1 \
    --robot.id=follower_arm \
    --robot.cameras="{ front: {type: opencv, index_or_path: 0, width: 640, height: 480, fps: 30, fourcc: "MJPG"}, wrist: {type: opencv, index_or_path: 2, width: 640, height: 480, fps: 30, fourcc: "MJPG"}}" \
    --teleop.type=so101_leader \
    --teleop.port=/dev/ttyACM0 \
    --teleop.id=leader_arm \
    --display_data=true \
    --dataset.repo_id=so101/clean_table \
    --dataset.num_episodes=8 \
    --dataset.single_task="Pick up and move the cup to the marker" \
    --dataset.push_to_hub=false \
    --dataset.episode_time_s=30 \
    --dataset.reset_time_s=5 \
    --resume=true


~/.cache/huggingface/lerobot/{repo-id}


Some tasks ideas:
- Pick up all pens and put them in the cup
- Pick up and move the cup to the marker
- Stack the cubes


====================================================================


lerobot-train \
  --dataset.repo_id=clean_table \
  --policy.type=act \
  --output_dir=outputs/train/act_clean_test \
  --job_name=act_clean_test \
  --policy.device=cuda \
  --policy.repo_id=rtx6000/my_groot_policy \
  --policy.push_to_hub=false

lerobot-train \
  --output_dir=outputs/train/act_clean_test \
  --save_checkpoint=true \
  --batch_size=8 \
  --steps=100000 \
  --save_freq=20000 \
  --log_freq=200 \
  --policy.push_to_hub=false \
  --policy.device=cuda \
  --policy.type=groot \
  --policy.repo_id=rtx6000/my_groot_policy \
  --policy.tune_diffusion_model=false \
  --dataset.repo_id=clean_table \
  --wandb.enable=false \
  --job_name=groot_clean_test


====================================================================


