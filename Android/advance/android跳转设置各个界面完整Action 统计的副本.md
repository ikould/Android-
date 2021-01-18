# 快写
### 获取状态
```
StuQuickWriteManger.getInstance().isOpenQuickWrite();
```
### 实现监听
```
private OnQuickWriteHelper.OnQuickWriteListener quickWriteListener = new OnQuickWriteHelper.OnQuickWriteListener() {
@Override
public void onResult(boolean isOpen) {

}
};
StuQuickWriteManger.getInstance().addQuickWriteListener(quickWriteListener);
StuQuickWriteManger.getInstance().removeQuickWriteListener(quickWriteListener);
```

# 小调查
### 获取状态
```
SurveyManager.getInstance().isOpenSurvey();
```
### 实现监听
```
private OnSurveyHelper.OnSurveyListener mSurveyListener = new OnSurveyHelper.OnSurveyListener() {
@Override
public void onResult(boolean isOpen) {
}
};
SurveyManager.getInstance().addSurveyListener(mSurveyListener);
SurveyManager.getInstance().removeSurveyListener(mSurveyListener);
```
# 快答
### 获取状态
```
QuickAnswerManager.getInstance().isOpenQuickAnswer();
```
### 实现监听
```
private OnQuickAnswerHelper.OnQuickAnswerListener mQuickAnswerListener = new OnQuickAnswerHelper.OnQuickAnswerListener() {
@Override
public void onResult(boolean isOpen) {
}
};
QuickAnswerManager.getInstance().addQuickAnswerListener(mQuickAnswerListener);
QuickAnswerManager.getInstance().removeQuickAnswerListener(mQuickAnswerListener);
```

# 音答
> 找袁嘉俊
# 短音
> 找赵豪
