## Kaggle-ის კონკურსის მოკლე მიმოხილვა

ამ კონკურსში საჭიროა წინასწარ განვსაზღვროთ საცხოვრებელი სახლების საბოლოო ფასები, გამოყენებული მონაცემებიდან. მონაცემებში მოცემულია 79 ცვლადი, რომლებიც გავლენას ახდენს ღირებულებაზე (მაგალითად, საერთო ფართი, მეტრიკები ოთახების რაოდენობის შესახებ, უბანი და სხვა).

## მიდგომა პრობლემის გადასაჭრელად

ჩემი მიდგომა მოიცავდა რამდენიმე ძირითად ეტაპს:

1. მონაცემების გასუფთავება (Missing Values, Outliers)
2. Feature Engineering (ახალი ცვლადების შექმნა, მაგალითად TotalSF)
3. რამდენიმე მოდელის გაშვება და შედარება (Linear, RandomForest, XGBoost)
4. საუკეთესო მოდელის შერჩევა ვალიდაციაზე (RMSE კრიტერიუმი)
5. საბოლოო პროგნოზი Kaggle-ის ტესტ სეტზე

## რეპოზიტორიის სტრუქტურა

- **model_experiment.ipynb**: მონაცემების გასუფთავება, Feature Engineering, სხვადასხვა მოდელის ტრეინინგი,სხვადასხვა ჰიპერპარამეტრების კომბინაციებით და MLflow-ზე ლოგირება
- **model_inference.ipynb**: საუკეთესო მოდელის ჩამოტვირთვა Model Registry-დან და ტესტ სეტზე საბოლოო პროგნოზის შექმნა (submission.csv) - score [**0.12908**](https://www.kaggle.com/competitions/house-prices-advanced-regression-techniques/submissions#)

Feature Engineering

- **კატეგორიული ცვლადების რიცხვითში გადაყვანა**: `get_dummies()` გამოვიყენეთ, რომ ყველა category გადავიდეს 0/1 სვეტებში.
- **Nan მნიშვნელობების დამუშავება**:
    
    Median-ით შევსება, `GarageType` = "None", თუ აკლია.
    
- **Cleaning მიდგომები**: ამოვიღეთ Outlier-ები (მაგალითად, ძალიან დიდი `GrLivArea`).
- დავამატე ბევრი ახალი ცვლადი, მაგალითად Age of the house when sold, Years since renovation, Whether house was remodeled, Total bathrooms (combining full and half baths), Living area per bedroom, Kitchen quality with area, Season sold.

## Training

- **ტესტირებული მოდელები**:
    1. Linear Regression (ბაზისური მოდელი)
    2. Random Forest
    3. XGBoost
    4. Ensemble (სხვადასხვა მოდელების შერწყმა)
- **Hyperparameter ოპტიმიზაციის მიდგომა**:
    - GridSearchCV (n_estimators, max_depth, learning_rate)
- **საბოლოო მოდელის შერჩევის დასაბუთება**:
    - საუკეთესო ვალიდაციის RMSE ამ მოდელმა მიიღო, დაბალი Error

## MLflow Tracking

- **MLflow ექსპერიმენტების ბმული**: [https://dagshub.com/enelene/ML-assignment-1.mlflow/#/experiments/0?searchFilter=&orderByKey=metrics.`rmse_val`&orderByAsc=true&startTime=ALL&lifecycleFilter=Active&modelVersionFilter=All+Runs&datasetsFilter=W10%3D](https://dagshub.com/enelene/ML-assignment-1.mlflow/#/experiments/0?searchFilter=&orderByKey=metrics.%60rmse_val%60&orderByAsc=true&startTime=ALL&lifecycleFilter=Active&modelVersionFilter=All+Runs&datasetsFilter=W10%3D)
- **მეტრიკების აღწერა**: RMSE (log(SalePrice)) ვალიდაციაზე
- **საუკეთესო მოდელის შედეგები**: RMSE_val = 0.137

## შეფასება

- ვალიდაციის RMSE ~ 0.14, Kaggle-ზე_Public_Leaderboard ~ 0.13.
