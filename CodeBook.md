##Codebook for data_tidy



Variable name    | Description
-----------------|------------
activityName     | Activity name which can be WALKING, WALKING_UPSTAIRS,WALKING_DOWNSTAIRS,SITTING,STANDING,LAYING
subject          | Subject ID is an interger within 1 ~30
domain           | Data colletion domain. "t" stands for time domain, "f" stands for frequency domain.
accelerationType | Acceration type which can be "Body" or "Gravity"
signalType       | Signal type which can be "Acc", "AccJerk", "Gyro", "GyroJerk".
axis             | Axis which can be "X", "Y", "Z", "Mag". Note that we consider "Mag" as a special axis in the measure.
mathMethod       | Math mothods that applied to measurements. In this case, it can be "mean()" or "std()".
value            | Mean value for the measurements from those math methods.

