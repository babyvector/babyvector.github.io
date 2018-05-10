---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
---
#<center>深入FOR循环</center>
<p align = "right">yongkangxuuest@gmail.com</p>
<p align = "right">4.22.2018</p>
-----
	//这个一个AL7_37程序的部分，sort_index = 2的时候将列表中的数据按照字符串的非降序
	//排列，如果两个字符串相同则按照学号的大小进行排序
	void excel(rootContainer&student_information_container,int sort_index){
		if (sort_index == 2){
			qsort<string>(student_information_container, 0, student_information_container.size() - 1, 2);
			std::reverse(student_information_container.begin(),student_information_container.end());
			int start_pos = 0;
			cout << "student_information_container:"<<student_information_container.size() << endl;
			for (start_pos = 0; start_pos <= student_information_container.size() - 2; start_pos++){
				/*还是在for循环这里卡了1个小时
				  就是因为for循环中
				  少了一个=
				  NOTE！！！
				  for循环的逻辑;
				  第一次：初始化 start_pos = 0,检查条件：start_pos <= student_information_container.size() - 2；
				  之后N次：start_pos++,检查条件：start_pos <= student_information_container.size() - 2;
				  NOT->先检查条件在自加加，那样检查条件的意义就是不存在的。
				*/
				cout << "before:" << start_pos << endl;
				int end_pos = start_pos+1;
				
				while(student_information_container[start_pos].student_name.name == student_information_container[end_pos].student_name.name){
					if (++end_pos < student_information_container.size()){}
					else{
						break;
					}
				}
				
				cout << "start_pos:" << start_pos << " " << "end_pos:" << end_pos << endl;
				
				if (end_pos - 1 != start_pos){
					cout << "in the innerSort():" << endl;
					cout<<student_information_container[start_pos].student_name.name << endl;
					qsort<int>(student_information_container, start_pos, end_pos - 1, 1);
					start_pos = end_pos - 1;
					//start_pos = end_pos;
				}
				else{
					continue;
				}
					
			}
		}
		else{
			qsort<int>(student_information_container,0,student_information_container.size()-1,sort_index);
		}
	}
