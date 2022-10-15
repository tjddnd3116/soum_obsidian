# red black tree란?
>	자가 균형 이진 탐색트리
트리에서 노드의 삽입과 삭제 같은 연산이 일어날 때 자동으로 균형트리를 유지하는 이진탐색 트리이다.

# red black tree의 정의
1. 각 노드의 색은 red 또는 black 이다.
2. root 노드는 black 이다.
3. 모든 말단 노드는 black 이다.
4. red 노드의 자식들은 전부 black 이다. (red 노드는 연속되어 등장 할 수 없다.)
5. root 노드에서 시작해서 말단노드에 이르는 모든 경로에는 동일 개수의 black 노드가 존재한다.

# red black tree의 rotate
![[42/ft_containers/red black tree rotate.jpg]]

# red black tree의 삽입
	문제 발생의 경우
	1. 조건 2 위반
			기준 노드가 red color 이면서 root 노드이다.
			-> 기준노드를 black color로 바꿔준다.
	2. 조건 4 위반
			기준 노드와 부모노드 둘다 red이다.
			case1. 기준노드의 삼촌 노드가 red 인경우
					기준노드의 부모노드와 삼촌노드를 black으로 바꿔주고 할아버지노드를 red로 바꿔준다.
					하지만 red-red 문제가 위로 2칸 이동해 있을 수 있다.
					그러면 기준노드를 할아버지 노드로 변경해 재귀적으로 문제를 해결한다.
					
			case2. 기준 노드의 삼촌노드가 black인 경우(leaf node 포함)
					case 2-1. 부모노드가 할아버지 노드의 왼쪽 자식일 경우
						case 2-1-1. 기준 노드가 오른쪽 자식일 경우
								부모노드를 left-rotate 한뒤 기준노드를 부모노드로 바꿔준다.
								-> case 2-1-2 상황으로 만듬
								그다음 case 2-1-2를 진행한다.
								
						case 2-1-2. 기준노드가 왼쪽 자식일 경우
								부모노드를 black, 할아버지 노드를 red로 바꾼다.
								할아버지 노드를 기준으로 right-rotate를 진행한다.
								
					case 2-2. 부모노드가 할아버지 노드의 오른쪽 자식일 경우
						case 2-2-1. 기준노드가 왼쪽 자식일 경우
								부모노드를 right-rotate 한뒤 기준노드를 부모노드로 바꿔준다.
								-> case 2-2-2상황으로 만듬
								
						case 2-2-2. 기준노드가 오른쪽 자식일 경우
								부모노드를 black, 할아버지 노드를 red로 바꾼다.
								할아버지 노드를 기준으로 left-rotate를 진행한다.
case 1 :
![[insert case 1.jpg]]
case 2-1 :
![[insert case 2-1.jpg]]
case 2-2
![[insert case 2-2.jpg]]
example :
![[red black tree insert test.jpg]]
# red black tree의 삭제
	1. 삭제한 노드가 red color 이다.
			-> red 노드의 자식과 부모는 black일 것이므로 red를 지워도 black-black 연속은 문제 되지 않는다.
	2. 삭제한 노드가 black color 이다.
			-> 조건 4 위반
					기준 노드와 부모 노드가 red color 이다.
					-> 기준노드를 black color로 바꾼다.
					
			-> 조건 5 위반
					root 노드에서 leaf노드 까지의 black노드의 개수가 다르다.
					기준노드가 black color 라서 이중흑색노드가 된다.
					case 1. 기준노드가 부모의 왼쪽 자식이다.
							case 1-1. 형제노드가 red color 이다.
									형제노드를 black으로 부모노드를 red로 변경한뒤
									부모노드 기준으로 left-rotate를 진행한다.
									이후 case 1-2, 1-3, 1-4로 진행한다.
									
							case 1-2. 형제노드가 black, 형제노드의 자식들도 black이다.
									형제노드를 red로 바꾼뒤 부모노드를 기준노드로 바꾼다.
									
							case 1-3. 형제노드가 black, 형제노드의 왼쪽 자식이 red이다.
									형제노드를 red로 바꾼뒤 형제노드의 왼쪽 자식을 black으로 변경
									형제노드를 기준으로 right-rotate를 적용한다.
									기준노드의 새로운 형제노드의 오른쪽 자식이 red이다.
									-> case 1-4에 해당하는 상황
									
							case 1-4. 형제노드가 black, 형제노드의 오른쪽 자식이 red이다.
									부모노드와 형제노드의 색을 바꾼다.
									형제노드의 오른쪽 자식을 black으로 바꾼다.
									부모노드 기준으로 left-rotate를 적용한다.
									
					case 2. 기준노드가 부모의 오른쪽 자식이다.
							case 2-1. 형제노드가 red color이다.
									형제노드를 black으로 부모노드를 red로 변경한다.
									부모노드 기준으로 right-rotate를 진행한다.
									이후 case 2-2, 2-3, 2-4로 진행한다.
									
							case 2-2. 형제노드가 black, 형제노드의 자식들도 black이다.
									형제노드를 red로 바꾼뒤 부모노드를 기준노드로 바꾼다.
									
							case 2-3. 형제노드가 black, 형제노드의 오른쪽 자식이 red이다.
									형제노드를 red로 바꾼뒤 형제노드의 오른쪽 자식을 red로 변경
									형제노드를 기준으로 left-rotate를 적용한다.
									기준노드의 새로운 형제노드의 왼쪽 자식이 red이다.
									-> case 2-4에 해당하는 상황
									
							case 2-4. 형제노드가 black, 형제노드의 왼쪽 자식이 red이다.
									부모노드와 형제노드의 색을 바꾼다.
									형제노드의 왼쪽 자식을 black으로 바꾼다.
									부모노드 기준으로 right-rotate를 적용한다.
![[Red black tree erase case.jpg]]
#ft_containers [[set]] [[map]] 