# Codes

- A1 - DFS BFS
    
    ```cpp
    #include <iostream>
    #include <vector>
    #include <queue>
    
    using namespace std;
    
    const int N = 1e5 + 2;
    bool vis[N];
    vector<int> adj[N];
    queue<int> q;
    
    void setVistedtoZero(bool vis[])
    {
        for (int i = 0; i <= N; i++)
        {
            vis[i] = false;
        }
    }
    
    void DFS(int node)
    {
        vis[node] = 1;
        cout << node << " ";
    
        vector<int>::iterator it;
    
        for (it = adj[node].begin(); it != adj[node].end(); it++)
        {
            if (vis[*it])
                ;
            else
            {
                DFS(*it);
            }
        }
    }
    
    void BFS()
    {
        if(q.empty())
            return ;
        //deque front and print 
        int node = q.front();
        q.pop();
        cout<<node<<" ";
        vector<int>::iterator it;
        for(it = adj[node].begin(); it!= adj[node].end();it++)
        {
            if(!vis[*it]){
                vis[*it]=true;
                q.push(*it);
            }
        }
        BFS();
    }
    
    int main()
    {
        int n, m;
        cout << "Enter no. of Edges and Vertices: \n";
        cin >> n >> m;
    
        cout << "Enter starting and ending vertices: \n";
        int x, y;
        for (int i = 0; i < n; i++)
        {
            cin >> x >> y;
            adj[x].push_back(y);
            adj[y].push_back(x);
        }
    
        int choice;
        do
        {
            cout << "\nCHOICES :-\n1 - DFS\n2 - BFS \n3 - Exit\n";
            cout << "Enter the choice : \n";
            cin >> choice;
    
            int node;
    
            switch (choice)
            {
            case 1:
                cout << "Enter the node to start with : \n";
                cin >> node;
                cout << "DFS: \n";
                setVistedtoZero(vis);
                DFS(node);
                break;
            case 2:
                cout << "Enter the node to start with : \n";
                cin >> node;
                q.push(node);
                cout << "BFS: \n";
                setVistedtoZero(vis);
                vis[node] = true;
                BFS();
                break;
            }
        } while (choice != 3);
    
        return 0;
    }
    ```
    
- A2 - A* Search
    
    ```python
    import numpy as np
    
    class Node:
        """
            A node class for A* Pathfinding
            parent is parent of the current Node
            position is current position of the Node in the maze
            g is cost from start to current Node
            h is heuristic based estimated cost for current Node to end Node
            f is total cost of present node i.e. :  f = g + h
        """
    
        def __init__(self, parent=None, position=None):
            self.parent = parent
            self.position = position
    
            self.g = 0
            self.h = 0
            self.f = 0
    
        def __eq__(self, other):
            return self.position == other.position
    
    # This function return the path of the search
    def return_path(current_node, maze):
        path = []
        no_rows, no_columns = np.shape(maze)
        # here we create the initialized result maze with -1 in every position
        result = [[-1 for i in range(no_columns)] for j in range(no_rows)]
        current = current_node
        while current is not None:
            path.append(current.position)
            current = current.parent
        # Return reversed path as we need to show from start to end path
        path = path[::-1]
        start_value = 0
        # we update the path of start to end found by A-star serch with every step incremented by 1
        for i in range(len(path)):
            result[path[i][0]][path[i][1]] = start_value
            start_value += 1
        return result
    
    def search(maze, cost, start, end):
        """
            Returns a list of tuples as a path from the given start to the given end in the given maze
            :param maze:
            :param cost
            :param start:
            :param end:
            :return:
        """
    
        # Create start and end node with initized values for g, h and f
        start_node = Node(None, tuple(start))
        start_node.g = start_node.h = start_node.f = 0
        end_node = Node(None, tuple(end))
        end_node.g = end_node.h = end_node.f = 0
    
        # Initialize both yet_to_visit and visited list
        # in this list we will put all node that are yet_to_visit for exploration.
        # From here we will find the lowest cost node to expand next
        yet_to_visit_list = []
        # in this list we will put all node those already explored so that we don't explore it again
        visited_list = []
    
        # Add the start node
        yet_to_visit_list.append(start_node)
    
        # Adding a stop condition. This is to avoid any infinite loop and stop
        # execution after some reasonable number of steps
        outer_iterations = 0
        max_iterations = (len(maze) // 2) ** 10
    
        # what squares do we search . serarch movement is left-right-top-bottom
        # (4 movements) from every positon
    
        move = [[-1, 0],  # go up
                [0, -1],  # go left
                [1, 0],  # go down
                [0, 1]]  # go right
    
        """
            1) We first get the current node by comparing all f cost and selecting the lowest cost node for further expansion
            2) Check max iteration reached or not . Set a message and stop execution
            3) Remove the selected node from yet_to_visit list and add this node to visited list
            4) Perform Goal test and return the path else perform below steps
            5) For selected node find out all children (use move to find children)
                a) get the current position for the selected node (this becomes parent node for the children)
                b) check if a valid position exist (boundary will make few nodes invalid)
                c) if any node is a wall then ignore that
                d) add to valid children node list for the selected parent
    
                For all the children node
                    a) if child in visited list then ignore it and try next node
                    b) calculate child node g, h and f values
                    c) if child in yet_to_visit list then ignore it
                    d) else move the child to yet_to_visit list
        """
        # find maze has got how many rows and columns
        no_rows, no_columns = np.shape(maze)
    
        # Loop until you find the end
    
        while len(yet_to_visit_list) > 0:
    
            # Every time any node is referred from yet_to_visit list, counter of limit operation incremented
            outer_iterations += 1
    
            # Get the current node
            current_node = yet_to_visit_list[0]
            current_index = 0
            for index, item in enumerate(yet_to_visit_list):
                if item.f < current_node.f:
                    current_node = item
                    current_index = index
    
            # if we hit this point return the path such as it may be no solution or
            # computation cost is too high
            if outer_iterations > max_iterations:
                print("giving up on pathfinding too many iterations")
                return return_path(current_node, maze)
    
            # Pop current node out off yet_to_visit list, add to visited list
            yet_to_visit_list.pop(current_index)
            visited_list.append(current_node)
    
            # test if goal is reached or not, if yes then return the path
            if current_node == end_node:
                return return_path(current_node, maze)
    
            # Generate children from all adjacent squares
            children = []
    
            for new_position in move:
    
                # Get node position
                node_position = (current_node.position[0] + new_position[0], current_node.position[1] + new_position[1])
    
                # Make sure within range (check if within maze boundary)
                if (node_position[0] > (no_rows - 1) or
                        node_position[0] < 0 or
                        node_position[1] > (no_columns - 1) or
                        node_position[1] < 0):
                    continue
    
                # Make sure walkable terrain
                if maze[node_position[0]][node_position[1]] != 0:
                    continue
    
                # Create new node
                new_node = Node(current_node, node_position)
    
                # Append
                children.append(new_node)
    
            # Loop through children
            for child in children:
    
                # Child is on the visited list (search entire visited list)
                if len([visited_child for visited_child in visited_list if visited_child == child]) > 0:
                    continue
    
                # Create the f, g, and h values
                child.g = current_node.g + cost
                ## Heuristic costs calculated here, this is using eucledian distance
                child.h = (((child.position[0] - end_node.position[0]) ** 2) +
                           ((child.position[1] - end_node.position[1]) ** 2))
    
                child.f = child.g + child.h
    
                # Child is already in the yet_to_visit list and g cost is already lower
                if len([i for i in yet_to_visit_list if child == i and child.g > i.g]) > 0:
                    continue
    
                # Add the child to the yet_to_visit list
                yet_to_visit_list.append(child)
    
    if __name__ == '__main__':
        maze = [[0, 1, 0, 0, 0, 0],
                [0, 0, 0, 0, 0, 0],
                [0, 1, 0, 1, 0, 0],
                [0, 1, 0, 0, 1, 0],
                [0, 0, 0, 0, 1, 0]]
    
        start = [0, 0]  # starting position
        end = [3, 2]  # ending position
        cost = 1  # cost per movement
    
        path = search(maze, cost, start, end)
        print('\n'.join([''.join(["{:" ">3d}".format(item) for item in row])
                         for row in path]))
    ```
    
- A3 - Selection Sort - Greedy
    
    ```python
    # Implement Greedy Search Algorithm for: Selection Sort
    
    def sort(arr):
        for i in range(0,len(arr)):
            minIndex = i
            for j in range(i+1,len(arr)):
                if(arr[minIndex]> arr[j]):
                    minIndex = j
            arr[i], arr[minIndex] = arr[minIndex], arr[i]
        print(arr)
    
    arr = [5,2,1,3,4]
    sort(arr)
    ```
    
- A4 - N-Queens
    
    ```python
    def solve(board, col):
        #base case - All queens are placed
        if col>=n:
            return True
        # Try to place the queen in this column
        for i in range(n):
            if isSafe(board,i,col):
                #place the queen 
                board[i][col] = 1
                #recursively place the rest of the queens
                if solve(board,col+1) == True:
                    return True
                #This segment means the above placement does not lead to a solution
                board[i][col] = 0
            #Queen can not be placed in any row
        return False
    
    def isSafe(board, row, col):
        # Check for queens to the left of position in the row
        for i in range(col):
            if board[row][i] == 1:
                return False
        #Check for upper diagonal threats on the left side
        for i, j in zip(range(row,-1,-1),range(col,-1,-1)):
            if board[i][j] == 1:
                return False
        # check for lower diagonal threats on the left side
        for i, j in zip(range(row,n,1),range(col,-1,-1)):
            if board[i][j] == 1:
                return False
        
        return True
        
            
    
    print("Enter the value of n:")
    global n
    n = int(input())
    
    board = [[0 for x in range(0,n)] for b in range(0,n)]
    
    result = solve(board,0)
    if(result==True):
        for i in range(n):
            for j in range(n):
                print(board[i][j], end=" ")
            print()
    else:
        print("no solution")
    ```
    
- A5 - Chatbot
    
    ```python
    # 31154 - Pranav Mohril
    # ASSIGNMENT 5 - Develop an elementary chatbot for a suitable customer interaction application.
    
    import nltk
    from nltk.chat.util import *
    
    # Checking for specific keywords in the user input and giving an appropriate response
    pairs = [
        # ------- Basic conversation --------
        [
            (r"(.*)my name is(.*)"),
            ["Hello%2, how are you today?",]
        ],
        [
            (r'(.*)fine(.*)|(.*)good(.*)|(.*)well(.*)'),
            ["Good to know, how can I help you?",]
        ],
        [
            (r'(.*)hi(.*)|(.*)hey(.*)|(.*)hello(.*)'),
            ['Hey there, how can I help you?', 'Hi! how can I be of your assistance?', 'Hello, what can I help you with?']
        ],
        [
            (r'(.*)help(.*)|(.*)show(.*)'),
            ["Sure, I can help you with it.", "Sure, I will show it right away",]
        ],
        [
            (r'(.*)thank you(.*)|(.*)thanks(.*)|(.*) ty (.*)'),
            ['Welcome!', 'You are Welcome!', 'Glad I could help!']
        ],
        # -------- Ticket booking ---------
        [
            (r'(.*)book(.*)|(.*)booking(.*)|(.*)reserve(.*)'),
            ['Please enter the date of travel: ']
        ],
        [
            (r'(.*)date (.*)'),
            ['Buses on %2 are available. Please enter the bus number according to our website: ']
        ],
        [
            (r'(.*)bus (.*)'),
            ['Thanks for choosing bus %2. Now please enter the seats you want to book: ']
        ],
        [
            (r'(.*)seats (.*)'),
            ['Thank you for booking%2. I will now take you to the payment section.']
        ],
    ]
    
    # ------------------------------------------------------------------------------
    
    def chatbot():
        print("Hello, Welcome to XYZ Travel Agency. How can I help you?")
        cb = Chat(pairs, reflections)
        cb.converse()
        
    chatbot()
    
    '''
    SAMPLE INPUT
    
    hey / hello
    my name is udayan
    i am doing well
    
    - i want to reserve a seat
    - book a ticket
    - need to make a booking
    
    bus b1
    date 24/06/2022
    seats s1 s2 s3
    
    thanks
    '''
    
    '''
    OUTPUT:
    Hello, Welcome to XYZ Travel Agency. How can I help you?
    >Hello xyz
    Hello, what can I help you with?
    
    >my name is Udayan
    Hello udayan, how are you today?
    
    >i am fine
    Good to know, how can I help you?
    
    >i need to book a ticket
    Please enter the date of travel: 
    
    >date 3/05/2022
    Buses on 3/05/2022 are available. Please enter the bus number according to our website: 
    
    >Bus B22
    Thanks for choosing bus b22. Now please enter the seats you want to book: 
    
    >seats S1 S2 S33 S45
    Thank you for bookings1 s2 s33 s45. I will now take you to the payment section.
    
    >thanks 
    You are welcome!
    '''
    ```
    
- A6 - Expert System
    
    ```python
    print('Welcome to COVID-19 Expert system')
    covidSuspisionCounter=0
    
    severity=0
    asym=0
    oxylevel=0
    temp=0
    questions=['What is your body temparature','What is your oxygen level','How many vaccines have you taken','What is your age']
    yesnoqs=['Do you have cough and cold','Are you able to recognize smell and taste','Are you suffering from sore throat','Are you suffering from headache','Are you suffering from BP/ diabetes','Have you come in a contact of a Covid suspicious person']
    for i in range(6):
        print(yesnoqs[i])
        print()
        ans=input()
        if(i!=1 and ans=='yes'):
            covidSuspisionCounter+=1
        elif(i==1 and ans=='no'):
            covidSuspisionCounter+=1
    for i in range(4):
        print(questions[i])
        print()
        if(i==0):
            ans=float(input())
            if(ans>=101.0):
                severity+=2
                covidSuspisionCounter+=1
                temp=1
            elif(ans<101.0 and ans>=99.6):
                severity+=1
            else:
                severity+=0
        if(i==1):
            ans=int(input())
            if(ans>=94):
                severity+=0
            elif(ans<94 and ans>87):
                severity+=1
            else:
                severity+=2
                covidSuspisionCounter+=1
                oxylevel=1
        if(i==2):
            ans=int(input())
            if(ans==0):
                severity+=2
            elif(ans==1):
                severity+=1
            else:
                severity+=0
        if(i==3):
            ans=int(input())
            if(ans>12 and ans<31):
                severity+=0
            elif(ans>31 and ans<51):
                severity+=1
            else:
                severity+=2
    if(covidSuspisionCounter>3):
        print('The patient is probably covid positive')
        print()
        if(severity<3):
            print('It looks like the symptoms are mild\nhome quarantine')
        elif(severity>=3 and severity<6):
            print('The patient can get an admission in the general ward')
        else:
            print('The patient looks critical')
    else:
        print('It looks like patient is not Covid positive')
    print()
    if(oxylevel==1):
        print("Keep monitoring patient's oxygen level")
    if(temp==1):
        print("Keep monitoring patient's body temperature")
    ```
    
- A7 -