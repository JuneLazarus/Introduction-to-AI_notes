# 搜索

### DepthFirstSearch

dfs通常由递归实现，但系统在递归调用时，使用的也是栈这一数据结构

基于栈操作的dfs

	function DepthFirstSearch(problem) returns a solution, or failure
    // 初始化
    node ← 节点(状态=problem.初始状态, 父节点=空, 路径代价=0)
    frontier ← 栈                        // LIFO
    将node压入frontier
    explored ← 空集合                    // 记录已扩展的状态
    loop do
        if frontier为空 then return failure
        
        node ← 从frontier弹出栈顶元素      // 最近加入的节点
        if problem.是目标状态(node.状态) then return 解决方案(node)
        
        将node.状态加入explored
        
        for each action in problem.动作(node.状态) do
            child ← 子节点(状态=problem.转移(node.状态, action),
                           父节点=node,
                           路径代价=node.路径代价 + 动作代价)
            if child.状态不在explored且不在frontier中 then
                将child压入frontier        // 子节点按逆序加入可实现某种顺序

函数递归的dfs

	function RecursiveDFS(node, problem, explored) returns a solution, or failure
    if problem.是目标状态(node.状态) then return 解决方案(node)
    
    将node.状态加入explored
    
    for each action in problem.动作(node.状态) do
        child ← 子节点(状态=problem.转移(node.状态, action),
                       父节点=node,
                       路径代价=node.路径代价 + 动作代价)
        if child.状态不在explored then
            result ← RecursiveDFS(child, problem, explored)
            if result ≠ failure then return result
    
    return failure

### BreadthFirstSearch

通常基于队列实现

	function BreadthFirstSearch(problem) returns a solution, or failure
    // 初始化
    node ← 节点(状态=problem.初始状态, 父节点=空, 路径代价=0)
    frontier ← 队列                              // FIFO
    将node加入frontier
    explored ← 空集合                            // 记录已扩展的状态

    loop do
        if frontier为空 then return failure
        
        node ← 从frontier中弹出队首元素
        if problem.是目标状态(node.状态) then return 解决方案(node)
        
        将node.状态加入explored
        
        for each action in problem.动作(node.状态) do
            child ← 子节点(状态=problem.转移(node.状态, action),
                           父节点=node,
                           路径代价=node.路径代价 + 动作代价)
            if child.状态不在explored且不在frontier中 then
                将child加入frontier队尾

### UniformCostSearch

通常基于优先队列实现

	function UniformCostSearch(problem) returns a solution, or failure
    // 初始化：节点包含状态、父节点、路径代价
    node ← 节点(状态=problem.初始状态, 父节点=空, 路径代价=0)
    frontier ← 优先队列，按节点的路径代价排序
    将node加入frontier
    explored ← 空集合          // 已扩展节点集合

    loop do
        if frontier为空 then return failure
        
        node ← 从frontier中弹出路径代价最小的节点  // 关键：总是选代价最小的
        if problem.是目标状态(node.状态) then return 解决方案(node)
        
        将node.状态加入explored
        
        for each action in problem.动作(node.状态) do
            child ← 子节点(状态=problem.转移(node.状态, action),
                           父节点=node,
                           路径代价=node.路径代价 + 动作代价)
            if child.状态不在explored且不在frontier中 then
                将child加入frontier
            else if child.状态已在frontier中且child.路径代价 < frontier中对应节点的路径代价 then
                用child替换frontier中的对应节点  // 找到更低代价路径，更新

```cpp
//UCS
struct cmp {
	bool operator()(const vector<int>& a, const vector<int>& b) {
		return a[1] > b[1];
	}
};

int main() {
	vector<vector<int>>coor = {
		{0,0}, //S
		{3,2}, //A
		{5,2}, //B
		{6,5}, //C
		{6,3}, //D
		{8,5} //T
	};

	vector<vector<vector<int>>>graph = { 
		{{1,4},{2,7}}, //S
		{{2,4},{3,5}}, //A
		{{4,5}},//B
		{{4,2},{5,6}}, //C
		{{5,3}}, //D
		{} //T
	};

	priority_queue<vector<int>, vector<vector<int>>, cmp>pq;
	pq.push({ 0,0 }); //位置编号，花费
	vector<int>dist(coor.size(), INT_MAX);
	vector<bool>visited(coor.size(), false);
	dist[0] = 0;
	int expanded_nodes = 0;
	while (!pq.empty()) {
		auto head = pq.top();
		pq.pop();
		int pos = head[0], cost = head[1];
		if (visited[pos]) continue;
		expanded_nodes++;
		if (pos == 5) break;
		visited[pos] = true;
		for (auto v : graph[pos]) {
			expanded_nodes++;
			int npos = v[0], ncost = v[1];
			if (dist[npos] > dist[pos] + ncost) {
				dist[npos] = dist[pos] + ncost;
				pq.push({ npos,dist[npos] });
			}
		}
	}
	cout << "Test for UCS:" << endl;
	cout << "minimum cost: " << dist[5] << endl;
	cout << "expanded nodes: " << expanded_nodes << endl;
	return 0;
}
```

### GreedyBestFirstSearch

	function GreedyBestFirstSearch(problem, heuristic) returns a solution, or failure
    // 初始化
    node ← 节点(状态=problem.初始状态, 父节点=空, 路径代价=0)
    frontier ← 优先队列，按启发式值h(节点状态)排序
    将node加入frontier
    explored ← 空集合

    loop do
        if frontier为空 then return failure
        
        node ← 从frontier中弹出启发式值最小的节点
        if problem.是目标状态(node.状态) then return 解决方案(node)
        
        将node.状态加入explored
        
        for each action in problem.动作(node.状态) do
            child ← 子节点(状态=problem.转移(node.状态, action),
                           父节点=node,
                           路径代价=node.路径代价 + 动作代价)
            if child.状态不在explored且不在frontier中 then
                将child加入frontier
            // 注意：贪婪搜索不会因发现更小h值而更新已在frontier中的节点，
            // 因为h只依赖于状态，同一状态无论路径如何h值相同。

### AStarSearch

	function AStarSearch(problem, heuristic) returns a solution, or failure
    // 初始化
    node ← 节点(状态=problem.初始状态, 父节点=空, 路径代价=0)
    frontier ← 优先队列，按f(n) = g(n) + h(n)排序
    将node加入frontier
    explored ← 空集合                      // 存储已扩展的状态
    // 可选：记录每个状态已找到的最小g值，用于更新
    g_costs ← 空字典                        // 记录每个状态当前已知的最小g值

    loop do
        if frontier为空 then return failure
        
        node ← 从frontier中弹出f值最小的节点
        
        if problem.是目标状态(node.状态) then return 解决方案(node)
        
        将node.状态加入explored
        g_costs[node.状态] ← node.路径代价
        
        for each action in problem.动作(node.状态) do
            child ← 子节点(状态=problem.转移(node.状态, action),
                           父节点=node,
                           路径代价=node.路径代价 + 动作代价)
            
            // 跳过已在explored中的状态
            if child.状态 in explored then continue
            
            // 计算child的g值
            new_g = child.路径代价
            
            // 如果child已在frontier中且新g值更小，则更新
            if child.状态 in frontier then
                if new_g < g_costs[child.状态] then
                    用child替换frontier中对应节点，并更新g_costs
            else
                将child加入frontier
                g_costs[child.状态] ← new_g

```cpp
//Astar
struct cmp {
	bool operator()(const vector<int>& a, const vector<int>& b) {
		return a[2] > b[2];
	}
};
int main() {
	vector<vector<int>>coor = {
		{0,0}, //S
		{3,2}, //A
		{5,2}, //B
		{6,5}, //C
		{6,3}, //D
		{8,5} //T
	};
	vector<vector<vector<int>>>graph = {
		{{1,4},{2,7}}, //S
		{{2,4},{3,5}}, //A
		{{4,5}},//B
		{{4,2},{5,6}}, //C
		{{5,3}}, //D
		{} //T
	};
	priority_queue<vector<int>, vector<vector<int>>, cmp>pq;
	pq.push({ 0,0,0 }); //位置编号，花费, 启发
	vector<int>dist(coor.size(), INT_MAX);
	vector<bool>visited(coor.size(), false);
	dist[0] = 0;
	int expanded_nodes = 0;
	while (!pq.empty()) {
		auto head = pq.top();
		pq.pop();
		int pos = head[0], cost = head[2];
		if (visited[pos]) continue;
		if(pos==5) break;
		visited[pos] = true;
		expanded_nodes++;
		for (auto v : graph[pos]) {
			int npos = v[0], ncost = v[1];
			if (dist[npos] > dist[pos] + ncost) {
				dist[npos] = dist[pos] + ncost;
				int heuristic = sqrt((coor[npos][0] - 8) * (coor[npos][0] - 8) + (coor[npos][1] - 5) * (coor[npos][1] - 5));
				pq.push({ npos, dist[npos]+heuristic, dist[npos] });
			}
		}
	}
	cout << "Test for Astar:" << endl;
	cout << "minimum cost: " << dist[5] << endl;
	cout << "expanded nodes: " << expanded_nodes << endl;
	return 0;
}
