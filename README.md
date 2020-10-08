# AI-Project [multiAgent]
## [Võ Lương Bằng](https://github.com/dawndie) - 18020198|K63J
## Link Youtube:

![Alt Text](https://media.giphy.com/media/Z71HSa4vBWlW/giphy.gif)

## Q1: Refex agent
### Grade:
```php
python autograder.py -q q1
```
### Mô tả: 
Trong mã nguồn được cung cấp đã có sẵn class ReflexAgent, việc của chúng ta bây giờ là viết hàm ước lượng (evaluation function) để làm cho Reflex Agent trở nên thông minh hơn, biết lựa chọn thức ăn và tránh ghost. Hàm Evaluation là hàm lấy vào GameState và trả ra 1 số, nếu số này càng lớn thì có nghĩa là tính hình đang thuật lợi, pacman đnag ở càng gần thức ăn còn nếu số này rất nhỏ thì nghĩa là pacman đang gặp nguy hiểm.

### Mã nguồn evaluation function :
```php
# focusing on eating food.When ghost near don't go,
        newFood = successorGameState.getFood().asList()
        minFoodist = float("inf")
        for food in newFood:
          minFoodist = min(minFoodist, manhattanDistance(newPos, food))
# avoid ghost if too close
        for ghost in successorGameState.getGhostPositions():
            if (manhattanDistance(newPos, ghost) < 2): 
                return -float('inf')

        return successorGameState.getScore() + 1.0/minFoodist
```
`newFood` - Danh sách chứa các thức ăn đang có trên bản đồ.
`minFoodist` - Khoảng cách đến thức ăn ngắn nhất.






