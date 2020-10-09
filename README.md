# AI-Project [multiAgent]
## [Võ Lương Bằng](https://github.com/dawndie) - 18020198|K63J
## Link Youtube:

![Alt Text](https://media.giphy.com/media/11UyDwkBKvmybe/giphy.gif)

## Q1: Refex agent
### Grade:
```php
python autograder.py -q q1
```
### Mô tả: 
Trong mã nguồn được cung cấp đã có sẵn class `ReflexAgent`, việc của chúng ta bây giờ là viết hàm ước lượng (evaluation function) để làm cho Reflex Agent trở nên thông minh hơn, biết lựa chọn thức ăn và tránh ghost. Hàm Evaluation là hàm lấy vào `GameState` và trả ra 1 số, nếu số này càng lớn thì có nghĩa là tính hình đang thuật lợi, pacman đang ở càng gần thức ăn còn nếu số này rất nhỏ thì nghĩa là pacman đang gặp nguy hiểm.

### Mã nguồn `evaluationFunction` :
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
👉`newFood` - Danh sách chứa các thức ăn đang có trên bản đồ.

👉`minFoodist` - Khoảng cách đến thức ăn ngắn nhất.

## Q2: Minimax
### Grade:
```php
python autograder.py -q q2
```
### Mô tả: 
Hoàn thành hàm `getAction` trong class `MinimaxAgent` có nhiệm vụ là lấy vào GameState và và đưa ra hướng tiếp theo mà pacman cần phải đi sử dụng thuật toán minimax, trong mã nguồn em tạo thêm 3 hàm nữa để hỗ trợ là `minimax`,`maxVal`,`minVal`
### Mã nguồn các hàm :
👉 Hàm`getAction`
```php
def getAction(self, gameState):
        return self.maxval(gameState, 0, 0)[0] # return next action for pacman
```
👉 Hàm `minimax`
```php
def minimax(self, gameState, agentIndex, depth):
        if depth is self.depth * gameState.getNumAgents() \
                or gameState.isLose() or gameState.isWin():
            return self.evaluationFunction(gameState) 
        if agentIndex is 0:
            return self.maxval(gameState, agentIndex, depth)[1] 
        else:
            return self.minval(gameState, agentIndex, depth)[1]
```

👉 Hàm `maxVal`
```php
def maxval(self, gameState, agentIndex, depth):
        bestAction = ("max",-float("inf"))
        for action in gameState.getLegalActions(agentIndex):
            succAction = (action,self.minimax(gameState.generateSuccessor(agentIndex,action),
                                      (depth + 1)%gameState.getNumAgents(),depth+1))
            bestAction = max(bestAction,succAction,key=lambda x:x[1])
        return bestAction
```
👉 Hàm `minVal`
```php
def minval(self, gameState, agentIndex, depth):
        bestAction = ("min",float("inf"))
        for action in gameState.getLegalActions(agentIndex):
            succAction = (action,self.minimax(gameState.generateSuccessor(agentIndex,action),
                                      (depth + 1)%gameState.getNumAgents(),depth+1))
            bestAction = min(bestAction,succAction,key=lambda x:x[1])
        return bestAction
```
👉 `agentIndex` = 0 là đến turn của pacman






