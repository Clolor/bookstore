#bookstore

### һ.���ܸ���
![image](https://github.com/zchdjb/bookstore/raw/master/WebContent/images/bookstore.png)<br/>

### ��.��ṹ

![image](https://github.com/zchdjb/bookstore/raw/master/WebContent/images/database.png)<br/>


##�ѵ�:
####1.�ڼ��ض�����ʱ��,�漰��OrderItem, Book��������,���ö���ѯ,�������ű�,��ֻ����MapListHandler����װ����,Ȼ������С����һ����װ���ݲ�ֳ���������,�ٰ���������ӳ���ϵ.<br/>



/**
	 * ͨ���û���uid������������,��ʱ������һ������
	 * @param uid
	 * @return
	 */
	public List<Order> findByUid(String uid){
		
		String sql = "select * from orders where uid=?";
			
		try {
			List<Order> orderList = qr.query(sql, new BeanListHandler<Order>(Order.class), uid);
			/*
			 * Ϊ����ض�����Ŀ��������,
			 * >>>>>>>>ͨ��oid
			 */
			for (Order order : orderList) {
				loadOrderItems(order);
			}
			return orderList;
		} catch (SQLException e) {
			throw new RuntimeException(e);
		} 
	}
	/**
	 * ͨ��oid���ض�����Ӧ������Ŀ
	 * ***���ö���ѯ
	 * ***�������ű�,��ʹ��MapListHandler����װ����
	 * 
	 * @param order
	 */
	private void loadOrderItems(Order order){
		//����ѯ
		String sql = "select * from orderitem o, book b where o.bid=b.bid and oid=?";
		try {
			List<Map<String, Object>> mapOrderItemList = qr.query(sql, new MapListHandler(), order.getOid());
			
			/*
			 * mapList����OrderItem, Book��������
			 * >>>��Ҫ��Ҫ��mapList��ֳ���������:OrderItem, Book
			 * >>>Ȼ����OrderItem��book��ӽ���,��������һ��OrderItem
			 * >>>���Ű�orderItem��ӵ�List<OrderItem>��
			 * >>>����ٰ�List<OrderItem>��ӵ�order������,������
			 */
			
			List<OrderItem> orderItemList = toOrderItemList(mapOrderItemList);//һ��map��Ӧ����һ��OrderItem����
			//�����������ж�����Ŀ��ӵ���Ӧ�Ķ�����
			order.setOrderItemList(orderItemList);
		} catch (SQLException e) {
			throw new RuntimeException(e);
		}
	}
	/**
	 * ��mapList����,�����ÿ��map��Ӧ����һ��OrderItem����
	 * ����OrderItem��ӵ�List<OrderItem>��
	 * @param mapList
	 * @return
	 */
	private List<OrderItem> toOrderItemList(List<Map<String, Object>> mapList) {
		//����һ��list,����װ��OrderItem����
		List<OrderItem> orderItemList = new ArrayList<OrderItem>();
		for (Map<String, Object> map : mapList) {
			OrderItem orderItem = toOrderItem(map);
			//�Ѷ�����Ŀ��ӵ�����������
			orderItemList.add(orderItem);
		}
		return orderItemList;
	}
	/**
	 * ��map���ɶ�Ӧ��OrderItem, Book��������,��������ϵ
	 * @param map
	 * @return
	 */
	private OrderItem toOrderItem(Map<String, Object> map) {
		OrderItem orderItem = CommonUtils.toBean(map, OrderItem.class);
		Book book = CommonUtils.toBean(map, Book.class);
		/*
		 * ��book��ӵ�������Ŀ��
		 */
		orderItem.setBook(book);
		return orderItem;
	}