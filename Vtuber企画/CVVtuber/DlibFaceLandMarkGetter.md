# DlibFaceLandMarkGetter

controller控制类初始化。

CVtuberProcess的setup方法里初始化，进入本脚本的Run方法

然后初始化Dlib库的FaceLandmarkDetector

```c#
    /**
    * Initializes a new instance of the FaceLandmarkDetector class.
    * <p>
    * <br>This instance uses default frontal face detector.
    * 
    * ObjectDetector is initialized in such a code.
    *   frontal_face_detector face_detector;
    *   face_detector = get_frontal_face_detector();
    * 
    * ShapePredictor is initialized in such a code.
    *   shape_predictor sp;
    *   deserialize(shape_predictor_filename) >> sp;
    * 
    * @param shapePredictorFilePath
    */	
    public FaceLandmarkDetector(string shapePredictorFilePath)
```