package lib280.tree;


import lib280.base.NDPoint280;
import lib280.exception.InvalidArgument280Exception;
import lib280.list.LinkedList280;


public class KDTree280 {
	private KDNode280 root;
	private int dim;
	
	/**
	 * *  Create a new kd-lib280.tree with specified dimension.
	 * 
	 * @param dim Dimension of points in the lib280.tree.
	 */
	public KDTree280(int dim) {
		root = null;
		this.dim = dim;
	}

	// It is also acceptable to have an additional constructor that accepts an array of points and calls insert()



	/**
	 * Partition the sub-array between points[left] and points[right] based on
	 * the pivot element points[right] using only dimension d.
	 * 
	 * @param points An array of N dimensional points
	 * @param left	lower index of the subarray to be partitioned
	 * @param right upper index of the subarray to be partitioned
	 * @param d dimension on which to partition
	 * @return index of the pivot element after partitioning
	 * 
	 * @precond The values in the i-th coordinate of all points in the input array are unique
	 * @postcond The subarray elements are rearranged so that all the elements for which dimension d
	 *           is smaller than dimension d of the pivot are first, followed by the pivot element,
	 *           followed by the elements for which dimension d is larger than dimension d of the pivot.
	 */
	
	private int partition(NDPoint280 points[], int left, int right, int d) {
		NDPoint280 temp;

		// Following the algorithm, obtain the pivot...
		double pivot = points[right].idx(d);

		// Scan from left to right...
		int swapIndex = left;
		for(int i=left; i < right; i++) {

			// If the point is less than the pivot
			if( points[i].idx(d) <= pivot) {
				// Move it to the left of the range (swapIndex)
				temp = points[swapIndex];
				points[swapIndex] = points[i];
				points[i] = temp;
				swapIndex++;
			}
		}

		// Swap the pivot into position.
		temp = points[swapIndex];
		points[swapIndex] = points[right];
		points[right] = temp;

		// Return the position of the pivot.
		return swapIndex;
	}
	
	
	/**
	 * Find the median of a subarray of NDPoints based on dimension d.
	 * 
	 * @param points An array of N Dimensional points
	 * @param left Lower index of the subarray for which to find the median in dimension d.
	 * @param right Upper index of the subarray for which to find the median in dimension d.
	 * @param d Dimension in which to find the median
	 * @param k The index of 'points' where you expect the k-th smallest element of 
	 *          the subarray points[left] through ponits[right] to be.
	 *
	 * @precond The values in the i-th coordinate of all points in the input array are unique.
	 * @postcond The subarray element points[(right-left)/2+left] contains the median element with respect to dimension d.
	 *           The elements in between index left ((right-left)/2+left)-1 are all smaller than the median, 
	 *           the elements in between index ((right-left)/2+left)+1 and right are all larger than the median,
	 *           all with respect to dimension d, but no ordering is guaranteed within those ranges.
	 *         
	 */
	private void findMedian(NDPoint280 points[], int left, int right, int d, int k)  {

		// Following the algorithm, if the sub-array is not empty...
		if( right > left ) {
			// Partition the sub-array
			int pivotIndex = partition(points, left, right, d);

			// If the pivot is larger than the k-th smallest element...
			if( pivotIndex > k)
				// Recurse on the left half of the sub-array
				findMedian(points, left, pivotIndex-1, d, k);
			else if( pivotIndex < k )
				// Otherwise, recurse on the right half of the sub-array.
				findMedian(points, pivotIndex +1, right, d, k);
		}
	}
	
	/**
	 * Build a kd-lib280.tree from an array of n-dimensional points
	 * 
	 * @param points Array of points to add to the kd-lib280.tree
	 * @param left lower index of subarray being added
	 * @param right upper index of subarray being added
	 * @param depth depth of the current node  being added
	 * @return returns the new node added (and consequently all it's children... really this is a subtree).
	 * 
	 * @precond The values in the i-th coordinate of all points in the input array are unique
	 */
	private KDNode280 insertkd(NDPoint280 points[], int left, int right, int depth) {

		// Following the algorithm, if the sub-array is empty, this is the base case -- do nothing and return.
		if(right < left) return null;

		// Otherwise, calculate the "axis" (dimension) that we are operating on at the current level of the tree.
		int axis = depth % this.dim;

		// Calculate the position where the median of the sub-array should lie.
		int median = (right-left)/2 + left;
		
		// Find the median of the points between left and right
		findMedian(points, left, right, axis, median);
		
		if( points[median].dim() != this.dim ) throw new IllegalArgumentException("Tried to insert a point with dimension != " + this.dim);
		
		// Use the median point as the point for the current node.
		KDNode280 newNode = new KDNode280(points[median], null, null);
		
		// Recursively insert the points between left and right that are smaller than the median
		newNode.setLeftNode(this.insertkd(points, left, median-1, depth+1));
		
		// Recursively insert the points between left and right that are larger than the median
		newNode.setRightNode(this.insertkd(points, median+1, right, depth+1));
		
		// Return the current subtree.
		return newNode;
		
	}
	
	/**
	 * Build a kd-lib280.tree from an array of N-dimensional points.
	 * 
	 * 
	 * @param points Array of points from which to construct the kd-lib280.tree.
	 * @precond All of the N-dimensional points in the input array must have dimension equal to the dimension of the kd-lib280.tree (this.dim).
	 * @precond The values in the i-th coordinate of all points in the input array are unique
	 *          NOTE: IF THIS CONDITION IS VIOLATED, THE ALGORITHMS WILL NOT WORK.
	 */
	public void insert(NDPoint280 points[]) {
		// We should verify the precondition here, but for this solution, it is acceptable if they did not.

		this.root = insertkd(points, 0, points.length-1, 0);
	}
	
	
	/**
	 * Helper method to do a range search of the lib280.tree.
	 * 
	 * @param curNode The current node in the search (initially the root, see lookup() method)
	 * @param lower The lower bounds on the coordinate ranges.
	 * @param upper The upper bounds on the coordinate ranges.
	 * @param found A list of points to which points that fall within the range are added.
	 * @param level The level of the lib280.tree where 'curNode' resides.
	 */
	private void lookupHelper(KDNode280 curNode, NDPoint280 lower, NDPoint280 upper, LinkedList280<NDPoint280> found, int level) {

		// Calculate the axis (dimension) that we are operating on at the current level of the tree.
		int axis = level % this.dim;

		// If we went below a leaf node, this is hte base case... return.
		if( curNode == null) return;

		// Set flag that assumes that the point in the current node is in range.
		boolean eq = true;
		
		NDPoint280 curItem = curNode.item();
		
		// Check each dimension of the point at this node, see if it is in range
		for(int i=0; i < this.dim && eq; i++) {
			if( curItem.compareByDim(i, lower) < 0 ||
					curItem.compareByDim(i, upper) > 0 ) {
				// point curNode is NOT range 
				eq = false; 
			}
		}
					
		// If we determined that the point in the current node is in range, add the point to the output list.
		if( eq ) found.insertFirst(curItem);
		
		// If the current dimension is greater than the lower bound, we have to
		// continue searching to the left.
		if( curItem.compareByDim(axis, lower) >= 0 ) {
				lookupHelper(curNode.leftNode(), lower, upper, found, level+1);
		}
		
		// If the current dimension is smaller than the upper bound, we have to search right.
		if( curItem.compareByDim(axis, upper) <= 0  ) {
				lookupHelper(curNode.rightNode(), lower, upper, found, level+1);
		}			
	}
	
	/**
	 * Perform a range search on the lib280.tree given the lower and upper bounds on each coordinate axis.
	 * 
	 * @param lower The lower bounds on the coordinate ranges.
	 * @param upper The upper bounds on the coordinate ranges.
	 * 
	 * @return A list of points in the lib280.tree that are in range.
	 * 
	 * @precond The dimensions of upper and lower must be the same as that of the lib280.tree (this.dim).
	 * @precond the i-th dimension of lower must be <= the ith-dimension of upper.
	 */
	public LinkedList280<NDPoint280> lookup(NDPoint280 lower, NDPoint280 upper) {
		LinkedList280<NDPoint280> found = new LinkedList280<NDPoint280>();

		// Verify the preconditions on lower and upper.
		if( lower.dim() != upper.dim() )
			throw new InvalidArgument280Exception("Lower and upper range points must have the same dimensionality.");

		for(int i=0; i < lower.dim(); i++) {
			if( lower.idx(i) > upper.idx(i) )
				throw new InvalidArgument280Exception("Lower range of dimension " + i + "is larger than upper range.");
		}

		// Invoke recursive helper.
		lookupHelper(this.root, lower, upper, found, 0);
		return found;
	}
	
	
	/**
	 * Helper method.  Prints a human readable version of the lib280.tree.
	 * @param rt The current node.
	 * @param i The level in the lib280.tree of the current node.
	 * @return A string representation of the subtree rooted at rt.
	 */
	protected String toStringByLevel(KDNode280 rt, int i) 
	{
		
		StringBuffer blanks = new StringBuffer((i - 1) * 5);
		for (int j = 0; j < i - 1; j++)
			blanks.append("     ");
	  
		String result = new String();
		if (rt != null && (rt.leftNode() != null || rt.rightNode() != null))
			result += toStringByLevel(rt.rightNode(), i+1);
		
		result += "\n" + blanks + i + ": " ;
		if (rt == null)
			result += "-";
		else 
		{
			result += rt.toString();
			if (rt.leftNode() != null || rt.rightNode() != null)
				result += toStringByLevel(rt.leftNode(), i+1);
		}
		return result;
	}

	/**
	 * 	String representation of the lib280.tree, level by level.
	 */
	public String toString()
	{
		return toStringByLevel(this.root, 1);
	}
	
	
	public static void main(String[] args) {

		// Testing 2D trees.
		
		// Set up some 2-D points.
		double[] q1 = { 5,2 };
		double[] q2 = { 9,10 };
		double[] q3 = { 11,1 };
		double[] q4 = { 4,3 };
		double[] q5 = { 2,12 };
		double[] q6 = { 3,7 };
		double[] q7 = { 1,5 };
		
		NDPoint280 q[] = new NDPoint280[7];
		q[0] = new NDPoint280(q1);
		q[1] = new NDPoint280(q2);
		q[2] = new NDPoint280(q3);
		q[3] = new NDPoint280(q4);
		q[4] = new NDPoint280(q5);
		q[5] = new NDPoint280(q6);
		q[6] = new NDPoint280(q7);
		//q[7] = new NDPoint280(q8);
		
		// Output the input points:
		System.out.println("Input 2D points:");
		for(int i=0; i < 7; i++)
			System.out.println(q[i]);
		
		// Build and display the lib280.tree.
		System.out.println("\nThe 2D lib280.tree built from these points is: ");
		KDTree280 T2 = new KDTree280(2);
		T2.insert(q);
		System.out.println(T2);		
		
		
		
		
		// Set up some 3D points
		
		double p1[] = {1,12,0};
		double p2[] = {18,1,2};
		double p3[] = {2,13,16};
		double p4[] = {7,3,3};
		double p5[] = {3,7,5};
		double p6[] = {16,4,4};
		double p7[] = {4,6,1};
		double p8[] = {5,5,17};
		
		NDPoint280 nd[] = new NDPoint280[8];
		
		nd[0] = new NDPoint280(p1);
		nd[1] = new NDPoint280(p2);
		nd[2] = new NDPoint280(p3);
		nd[3] = new NDPoint280(p4);
		nd[4] = new NDPoint280(p5);
		nd[5] = new NDPoint280(p6);
		nd[6] = new NDPoint280(p7);
		nd[7] = new NDPoint280(p8);
		
		// Print out the 3D points
		System.out.println("Input 3D points:");
		for(int i=0; i < 8; i++)
			System.out.println(nd[i]);

		
		
		KDTree280 T = new KDTree280(3);
		T.insert(nd);
		System.out.println(T);
		

		double lower[] = {0, 1, 0};
		double upper[] = {4, 6, 3};
		NDPoint280 L = new NDPoint280(lower);
		NDPoint280 U = new NDPoint280(upper);
		
		// First range search test of 3D lib280.tree
		System.out.println();
		System.out.println("Looking for points between " + L + " and " + U + ".");
		
		LinkedList280<NDPoint280> found = T.lookup(L, U);
		
		if( found.isEmpty() ) {
			System.out.println("No points in range.");
			return;
		}
		
		System.out.println("Found: ");
		
		found.goFirst();
		while(found.itemExists()) {
			System.out.println(found.item());
			found.goForth();
		}
		
		// Second range search test of 3D lib280.tree
		double lower2[] = {0, 1, 0};
		double upper2[] = {8, 7, 4};
		NDPoint280 L2 = new NDPoint280(lower2);
		NDPoint280 U2 = new NDPoint280(upper2);

		
		System.out.println();
		System.out.println("Looking for points between " + L2 + " and " + U2 + ".");
		
		found = T.lookup(L2, U2);
		
		if( found.isEmpty() ) {
			System.out.println("No points in range.");
			return;
		}
		
		System.out.println("Found: ");
		
		found.goFirst();
		while(found.itemExists()) {
			System.out.println(found.item());
			found.goForth();
		}
			
		
		// Second range search test of 3D lib280.tree
		double lower3[] = {0, 1, 0};
		double upper3[] = {17, 9, 10};
		NDPoint280 L3 = new NDPoint280(lower3);
		NDPoint280 U3 = new NDPoint280(upper3);

		
		System.out.println();
		System.out.println("Looking for points between " + L3 + " and " + U3 + ".");
		
		found = T.lookup(L3, U3);
		
		if( found.isEmpty() ) {
			System.out.println("No points in range.");
			return;
		}
		
		System.out.println("Found: ");
		
		found.goFirst();
		while(found.itemExists()) {
			System.out.println(found.item());
			found.goForth();
		}
			
	}
	

}
