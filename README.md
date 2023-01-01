Backward에서 NaN이 발생하거나 아래와 같은 에러 메시지를 띄우며 RuntimeError이 발생하는 경우가 있다.

RuntimeError: one of the variables needed for gradient computation has been modified by an inplace operation

해당 에러의 의미는 inplace operation이 있어 gradient를 계산하는 데 문제가 생겼다는 것이다. 보통 새로운 주소값을 가지는 텐서를 만들어주지 않고 텐서의 값 자체를 바꾸는 연산을 했을 때 발생한다. 그런데 여기서 주의해야 할 점은 +=이나 -=와 같은 Python 연산자들 모두 inplace operation이라는 것이다. 에러를 방지하려면 모두 풀어서 써주어야 한다.

```python
x += 1 # Inplace operation
x = x+1 # Not inplace operation
```
 

그럼 inplace operation이 정말 필요할 때는 어떻게 할까? Backward 계산 이후 torch.no_grad() 문 안에서 copy_ 함수를 통해 바꾸어주면 된다. 예를 들어 파라미터의 하한을 정해주고 싶을 때라면 아래처럼 하면 된다.

```python
with torch.no_grad():
    t.copy_ (t.data.clamp(min=1e-6))
```
 

PyTorch 연산은 대부분 새로운 주소를 할당해주어 inplace operation을 방지한다. 하지만 add_ 등 언더바가 붙어 있는 연산은 모두 inplace operation이다. 

```python
a = torch.tensor([0])
b = a.add(1) # Not inplace operation
b = a.add_(1) # Inplace operation
```
