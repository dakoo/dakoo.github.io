#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;
struct Node {
	int key;
	int value;
	Node *l_child;
	Node *r_child;
	Node(int k, int val){
		key = k;
		value = val;
		l_child = NULL;
		r_child = NULL;
	};
};
class BST {
private:
	Node *root;
	void removeTree(Node *tmp){
		if (tmp == NULL) return;
		if (tmp->l_child) removeTree(tmp->l_child);
		if (tmp->r_child) removeTree(tmp->r_child);
		delete tmp;
	}
	bool isbst(Node *r){
		if (r == NULL)
			return true;
		if (r->l_child && r->r_child){
			if (r->l_child->key >= r->key || r->r_child->key <= r->key) return false;
			return isbst(r->l_child) && isbst(r->r_child);
		} else if (r->l_child){
			if (r->l_child->key >= r->key) return false;
			return isbst(r->l_child);
		} else if (r->r_child){
			if (r->r_child->key <= r->key) return false;
			return isbst(r->r_child);
		}
		return true;
	}
	int size(Node *r){
		if (r == NULL) return 0;
		return size(r->l_child) + size(r->r_child) + 1;
	}
	int maxDepth(Node *r){
		if (r == NULL) return 0;
		int l_depth = maxDepth(r->l_child);
		int r_depth = maxDepth(r->r_child);
		return max(l_depth, r_depth) + 1;
	}
	void printPath(Node *r, vector<int> &T, int len){
		T[len] = r->key;
		if (!r->l_child && !r->r_child){
			for (int i = 0; i <= len; i++)
				cout << T[i] << " ";
			cout << endl;
			return;
		}
		if (r->l_child) printPath(r->l_child, T, len+1);
		if (r->r_child) printPath(r->r_child, T, len+1);
	};
public:
	BST(){
		root = NULL;
	}
	virtual ~BST(){
		removeTree(root);
	}
	void insertItem(int k, int val){
		if (root == NULL){
			root = new Node(k, val);
			return;
		}
		Node *tmp = root;
		Node *p = tmp;
		while (tmp != NULL){
			p = tmp;
			if (tmp->key > k){
				tmp = tmp->l_child;
			}
			else {
				tmp = tmp->r_child;
			}
		}
		if (p->key > k) {
			p->l_child = new Node(k, val);
		}
		else {
			p->r_child = new Node(k, val);
		}
	}
	bool isBST(){
		return isbst(root);
	}
	int size(){
		return size(root);
	}
	int maxDepth(){
		return maxDepth(root);
	}
	int minVal(){
		if (!root) return -1; //empty
		Node *temp = root;
		Node *p = root;
		while (temp != NULL){
			p = temp;
			temp = temp->l_child;
		}
		return p->key;
	}
	int maxVal(){
		if (!root) return -1; //empty
		Node *temp = root;
		Node *p = root;
		while (temp != NULL){
			p = temp;
			temp = temp->r_child;
		}
		return p->key;
	}
	void printAllPath(){
		vector<int> T(100, 0);
		int len = 0;
		printPath(root, T, len);
	}
};
int main(){
	BST b;
	b.insertItem(10, 100);
	b.insertItem(1, 200);
	b.insertItem(5, 300);
	b.insertItem(7, 100);
	b.insertItem(20, 400);
	b.insertItem(15, 500);
	b.insertItem(17, 600);
	b.insertItem(0, 700);
	/* 
	       10
	   1        20
	 0   5   15
	      7    17  
	*/
	/* check if the tree is a BST */
	if (b.isBST()) cout << "BST" << endl;	//must be "BST"
	else cout << "NOT BST" << endl;
	/* get the size of binary tree */
	cout << b.size() << endl; // must be 8
	/* get the maximum depth of binary tree*/
	cout << b.maxDepth() << endl; //msut be 4
	/* get the min & max value of binary search tree */
	cout << b.minVal() << endl; //must be 0
	cout << b.maxVal() << endl; //must be 20
	/* print all the possible paths in a binary tree*/
	b.printAllPath(); //must be 10 1 0, 10 1 5 7, 10 20 15 17
	/* Find the lowest common ancestor from two nodes in binary tree */
	return 0;
}

남은 것

getDiameter
getCommonAncestor
getDiamterNodes
