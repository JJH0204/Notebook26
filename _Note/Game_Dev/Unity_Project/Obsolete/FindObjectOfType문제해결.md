---
aliases:
  - "\bFindObjectOfType문제해결"
---
`FindObjectOfType<T>()`는 유니티 엔진 내의 모든 게임 오브젝트를 전수 조사하여 특정 컴포넌트를 찾기 때문에, 씬에 오브젝트가 많을수록 성능 부하가 매우 커진다. 특히 매 프레임 호출되거나 잦은 접근이 필요한 싱글톤 초기화에서는 지양해야 할 함수다.

유니티 2023.1버전 이후부터 이 함수가 실제로 [[_Note/Game_Dev/Unity_Project/Obsolete/Obsolete]] 경고를 띄우거나, 성능 최적화된 새로운 API로 대체할 것을 권장한다.

### 해결책1: 최신 유니티 권장 함수
- `FindAnyObjectByType<T>()`: 순서에 상관없이 아무거나 하나를 찾는다.
- `FindFirstObjectByType<T>()`: 씬 로드 순서상 가장 먼저 있는 것을 찾는다.

ex) 싱글톤은 어차피 하나만 존재해야 하므로, `FindAnyObjectByType<T>()`를 사용하는 것이 성능상 가장 유리하다.

### 해결책2: 탐색을 아예 하지않는 것
```csharp
protected virtual void Awake()
{
    if (_instance == null)
    {
        _instance = this as T;
        // 씬 전환 시 파괴되지 않게 하려면 (선택사항)
        // DontDestroyOnLoad(gameObject); 
    }
    else
    {
        Destroy(gameObject); // 중복 객체 파괴
    }
}
```
객체가 생성될 때 스스로 등록하면 비싼 담색 비용이 0이 된다.