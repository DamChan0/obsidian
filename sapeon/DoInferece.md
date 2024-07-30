```cpp
  virtual void DoInference()
  {
    SetInputData();
    ExecuteGraph();
    WaitInferenceDone();
    // const auto outputs = runtime_handle_->GetResult(context);
  }
```

SetInputData(); 
이 [[Initialize]] 에 이미 있지만 반복 하고 있으며 내부 에서 멤버 변수에 넣어주는 데이터도 바뀌지 않는 것을 확인 하였음

- SetInputData()를 지우고 동작시켰을때 차이를 확인하지 못함