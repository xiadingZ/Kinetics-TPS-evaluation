# Kinetics-TPS-evaluation
## Evaluation Criteria

The participants are required to provide two types of results. (1) Part State Parsing Result: For each frame in a test video, the participants should provide the predicted boxes of human instances, the predicted boxes of body parts as well as the predicted part state of each body part box. Note that, to reduce uploading burden, we will evaluate these results on the sampled frames of each test video (where the sampling interval is 5-frame). Hence, we encourage participants to provide the results on these frames. (2) Action Recognition Result: The participants should also provide the predicted action for each test video.

Since our goal is to leverage part state parsing for action recognition, we develop a new evaluation metric for this task, where we use Part State Correctness as condition for evaluating action recognition accuracy in a test video.

### Definition of Part State Correctness (PSC)

1. Matching humans in each frame: For each ground truth human box, we find the predicted human box according to IoU, where IoU between this prediction and ground truth box is maximum and should be > HUMAN_IOU_THRESH. If there is no matched prediction, the state correctness of all the body parts (w.r.t., this ground truth human box) is 0.
2. Matching body parts in each human: For a matched prediction of human box, we further compare the predicted boxes and ground truth of each body part. Suppose that, the number of predicted boxes is N_p for a body part. If at least one predicted box is overlapped with the ground truth part box (IOU > PART_IOU_THRESH) and the predicted state of this box has the same tag of the ground truth part, we set the state correctness of this part as 1/N_p.
We set HUMAN_IOU_THRESH as 0.5 and set PART_IOU_THRESH as 0.3.
3. Part State Correctness (PSC) of a test video: For each frame, we first average the state correctness of all the parts in this frame. Then, we can obtain PSC of a test video by averaging all frame-level results. Our PSC can reflect that how many parts can be reliably localized with correct state of gesture.

### Action Recognition Conditioned on PSC

1. For each test video, its video prediction can be seen as the correct one, if and only if its PSC is > PART_STATE_CORRECT_THRESH and its video prediction is the same as the ground truth action class. In this case, we can compute action recognition accuracy (top-1) under the condition of PART_STATE_CORRECT_THRESH.
2. We set PART_STATE_CORRECT_THRESH from 0 to 1, where the step size is 0.0001. Then we plot the curve of (PART_STATE_CORRECT_THRESH, the conditioned video accuracy). Then, we calculate the area under this curve as average video accuracy. We use this as the final evaluation metric (The result will be rounded up to 6 decimal places).
