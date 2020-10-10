# AI-Project [multiAgent] :v :v
## [Võ Lương Bằng](https://github.com/dawndie) - 18020198|K63J
## Link Youtube:

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
Hoàn thành hàm `getAction` trong class `MinimaxAgent` có nhiệm vụ là lấy vào GameState và và đưa ra hướng tiếp theo mà pacman cần phải đi sử dụng thuật toán minimax, trong mã nguồn em tạo thêm 3 hàm nữa để hỗ trợ là `minimax`,`maxVal`,`minVal`.
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

## Q3: Alpha-Beta Pruning
### Grade:
```php
python autograder.py -q q3
```
### Mô tả: 
Về cơ bản thì tác tử `AlphaBetaAgent` này tương đối giống với `MinimaxAgent` nhưng trong các hàm `minval` và `maxval` có thêm 2 dòng code để prunning.
### Mã nguồn các hàm:
👉 Hàm `getAction`
```php
def getAction(self, gameState):
        return self.maxval(gameState, 0, 0, -float("inf"), float("inf"))[0]
```

👉 Hàm `alphabeta` (tương đương với hàm minimax trong câu 2)
```php
def alphabeta(self, gameState, agentIndex, depth, alpha, beta):
        if depth is self.depth * gameState.getNumAgents() \
                or gameState.isLose() or gameState.isWin():
            return self.evaluationFunction(gameState)
        if agentIndex is 0:
            return self.maxval(gameState, agentIndex, depth, alpha, beta)[1]
        else:
            return self.minval(gameState, agentIndex, depth, alpha, beta)[1]
```

👉 Hàm `maxval`
```php
def maxval(self, gameState, agentIndex, depth, alpha, beta):
        bestAction = ("max",-float("inf"))
        for action in gameState.getLegalActions(agentIndex):
            succAction = (action,self.alphabeta(gameState.generateSuccessor(agentIndex,action),
                                      (depth + 1)%gameState.getNumAgents(),depth+1, alpha, beta))
            bestAction = max(bestAction,succAction,key=lambda x:x[1])

            # Prunning
            if bestAction[1] > beta: return bestAction
            else: alpha = max(alpha,bestAction[1])

        return bestAction
```   

👉 Hàm `minval`
```php
def minval(self, gameState, agentIndex, depth, alpha, beta):
        bestAction = ("min",float("inf"))
        for action in gameState.getLegalActions(agentIndex):
            succAction = (action,self.alphabeta(gameState.generateSuccessor(agentIndex,action),
                                      (depth + 1)%gameState.getNumAgents(),depth+1, alpha, beta))
            bestAction = min(bestAction,succAction,key=lambda x:x[1])

            # Prunning
            if bestAction[1] < alpha: return bestAction
            else: beta = min(beta, bestAction[1])

        return bestAction
```

## Q4: Expectimax
### Grade:
```php
python autograder.py -q q4
```
### Mô tả: 
`Expectimax` và `minimax` về cơ bản là giống nhau, chỉ khác nhau ở điểm là expectimax sẽ lấy giá trị trung bình ở các Node của ghost thay vì lấy min như minimax

### Mã nguồn các hàm:
👉 Hàm `getAction`
```php
 def getAction(self, gameState):
        # calling expectimax with the depth we are going to investigate
        maxDepth = self.depth * gameState.getNumAgents()
        return self.expectimax(gameState, "expect", maxDepth, 0)[0]
```
👉 Hàm `expectimax` tương đương với hàm minimax trong các câu trên
```php
def expectimax(self, gameState, action, depth, agentIndex):

        if depth is 0 or gameState.isLose() or gameState.isWin():
            return (action, self.evaluationFunction(gameState))

        # if pacman (max agent) - return max successor value
        if agentIndex is 0:
            return self.maxvalue(gameState,action,depth,agentIndex)
        # if ghost (EXP agent) - return probability value
        else:
            return self.expvalue(gameState,action,depth,agentIndex)
```

👉 Hàm `maxvalue`
```php
def maxvalue(self,gameState,action,depth,agentIndex):
        bestAction = ("max", -(float('inf')))
        for legalAction in gameState.getLegalActions(agentIndex):
            nextAgent = (agentIndex + 1) % gameState.getNumAgents()
            succAction = None
            if depth != self.depth * gameState.getNumAgents():
                succAction = action
            else:
                succAction = legalAction
            succValue = self.expectimax(gameState.generateSuccessor(agentIndex, legalAction),
                                        succAction,depth - 1,nextAgent)
            bestAction = max(bestAction,succValue,key = lambda x:x[1])
        return bestAction
```

👉 Hàm `expvalue`

```php
def expvalue(self,gameState,action,depth,agentIndex):
        legalActions = gameState.getLegalActions(agentIndex)
        averageScore = 0
        propability = 1.0/len(legalActions)
        for legalAction in legalActions:
            nextAgent = (agentIndex + 1) % gameState.getNumAgents()
            bestAction = self.expectimax(gameState.generateSuccessor(agentIndex, legalAction),
                                         action, depth - 1, nextAgent)
            averageScore += bestAction[1] * propability
        return (action, averageScore)
```






