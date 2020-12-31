##JAVA Web （Test）购物车

表设计

>DROP TABLE IF EXISTS product; // 如果数据库存在就删除，并创建。
    create table 'product' (
    'id' int(11) DEFAULT NULL,
    'name' varchar(50) DEFAULT NULL,
    'price' float DEFAULT NULL
    )ENGINE=InnoDB DEFAULT CHARSET=utf8;
    -- 产品表
 
>create table user(
    id int ,
    name varchar(50).
    password varchar(50)
) ENGIN=InnoDB DEFAULT CHARSET=utf8;
--用户表

>create table order (
    id int AUTO_INCREMENT,
    uid int  ,
    primary key (id)
);
--订单表 那个用户
 create table orderitem(
     id int AUTO_INCREMENT,
     pid int ,
     num int ,
     oid int ,
     primary key (id)
 );
-- 订单表 商品对应Id 订单Id

## MVC 设计模式

M 代表 模型（Model）
V 代表 视图（View）
C 代表 控制器（controller)

模型是什么呢？ 模型就是数据，就是dao,bean

视图是什么呢？ 就是网页, JSP，用来展示模型中的数据

控制器是什么？ 控制器用来把不同的数据，显示在不同的视图上。 在这个例子的，Servlet就是充当控制器的角色，把Hero对象，显示在JSP上。

控制器的作用就是把不同的数据(Model)，显示在不同的视图(View)上。


## Model Bean 部分


//用户表Baen

    public class User 
    {

	 int id;
	 String name;
	 String password;
	public int getId() 
    {
		return id;
	}
	public void setId(int id) 
    {
		this.id = id;
	}
	public String getName() 
    {
		return name;
	}
	public void setName(String name) 
    {
		this.name = name;
	}
	public String getPassword()
     {
		return password;
	}
	public void setPassword(String password) {
		this.password = password;
	}

    }

//产品表Bean

    public class Product {

	int id;
	String name;
	float price;

	public int getId()
    {
		return id;
	}
	public void setId(int id) 
    {
		this.id = id;
	}
	public String getName() 
    {
		return name;
	}
	public void setName(String name) 
    {
		this.name = name;
	}
	public float getPrice() 
    {
		return price;
	}
	public void setPrice(float price) 
    {
		this.price = price;
	}

    }

//购物车Bean

    public class OrderItem 
    {

	private int id;
	private Product product;
	private int num;
	private Order order;

    public Order getOrder() {
        return order;
    }
    public void setOrder(Order order) {
        this.order = order;
    }
	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	public Product getProduct() {
		return product;
	}
	public void setProduct(Product product) {
		this.product = product;
	}
	public int getNum() {
		return num;
	}
	public void setNum(int num) {
		this.num = num;
	}

    }

//订单Bean

    public class Order_ {
 
    int id;
    User user;
    public int getId()
     {
        return id;
    }
    public void setId(int id) 
    {
        this.id = id;
    }
    public User getUser() 
    {
        return user;
    }
    public void setUser(User user)
     {
        this.user = user;
    }

    }

## Model DAO 部分 

// 用户DAO

    public class UserDAO {


	public User getUser(String name,String password){
		User result=null;
		try{
			Class.forName("com.mysql.cj.jdbc.Driver");
			
			Connection c=DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/test?serverTimezone=UTC&characterEncoding=UTF-8",
                    "root", "123456");
			// SQL语句中，指定值用？代替
			String sql = "select * from user where name=? and password=?";
		
			PreparedStatement ps=c.prepareStatement(sql);
			
			// PreparedStatement的setX()系列方法，对指定的占位符设置实际的值
			ps.setString(1, name);
			ps.setString(2, password);
			// 执行PreparedStatement.executeQuery（查询）方法 executeUpdate(删除，更改，新增)方法 
			ResultSet rs=ps.executeQuery();
			// 新建一个Bean对象 User result =new User();  返回查询到的数据，存入Bean 对象中
			
            if(rs.next()){
				result=new User();
				result.setId(rs.getInt(1));
				result.setPassword(password);
				result.setName(name);
			}
			// 关闭 PreparedStatement
			ps.close();
			// 关闭 Connection
			c.close();
		}catch(ClassNotFoundException e){
			e.printStackTrace();
		}catch (SQLException e) {
			// TODO: handle exception
			e.printStackTrace();
		}
		// 返回 Bean User 对象
		return result;
	}
    }

// 产品DAO
public class ProductDAO {

    public Product getProduct(int id){
		Product result=null;
		
		//初始化 com.mysql.cj.jdbc.Driver
		try {
			Class.forName("com.mysql.cj.jdbc.Driver");
			
			//数据库连接，本地 3306端口，test数据库，UTC时区，UTF-8编码，用户名root ，密码 123456
			Connection c=DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/test?serverTimezone=UTC&characterEncoding=UTF-8",
	                "root", "123456");
			//查询产品表，按照Id 进行查询
			String sql = "select * from product where id = ?";
			// 创建PreparedStatement 对象
			PreparedStatement ps = c.prepareStatement(sql);
			
            ps.setInt(1, id);
            // 执行sql语句
            ResultSet rs = ps.executeQuery();
           
            // 处理结果集
            if (rs.next()) {
            	
                result = new Product();
                result.setId(id);
                // ResultSet.getString(2)第二列 
                // ResultSet.getFloat(3)第三列
                String name = rs.getString(2);
                float price = rs.getFloat(3);
                
                result.setName(name);
                result.setPrice(price);
 
            }
 
            ps.close();
 
            c.close();
 
        } catch (ClassNotFoundException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (SQLException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        return result;
    }
	
	
	public List<Product> ListProduct(){
		// 创建一个 product Bean类型，集合。
		List<Product> products=new ArrayList<Product>();
		try{
			//初始化 com.mysql.cj.jdbc.Driver
			Class.forName("com.mysql.cj.jdbc.Driver");
			
			//数据库连接，本地 3306端口，test数据库，UTC时区，UTF-8编码，用户名root ，密码 123456
			Connection c=DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/test?serverTimezone=UTC&characterEncoding=UTF-8",
                    "root", "123456");
			
			// 查询product 产品数据表，根据Id 进行降序 
			String sql = "select * from product order by id desc";
			//PreparedStatement 继承Statemen 
			PreparedStatement ps=c.prepareStatement(sql);
			// 返回resutltSet 结果集
			ResultSet rs=ps.executeQuery();
			
			//对结果集进行遍序
			while(rs.next()){
				//将得到数据暂时存入局部变量 
				Product product=new Product();
				int id=rs.getInt(1);
				String name=rs.getString(2);
				float price=rs.getFloat(3);
				//变量值存入bean
				product.setId(id);
				product.setName(name);
				product.setPrice(price);
				products.add(product);
			}
			
			ps.close();
			
			c.close();
		}catch( ClassNotFoundException e){
			e.printStackTrace();
		}catch (SQLException e) {
			// TODO: handle exception
			e.printStackTrace();
		}
		// 返回集合
		return products;
		
	}
	
    }


//订单 

	public class OrderDAO {

	    public void insert(Order o) {
	 
	        try {
	        	Class.forName("com.mysql.cj.jdbc.Driver");
				
				//数据库连接，本地 3306端口，test数据库，UTC时区，UTF-8编码，用户名root ，密码 123456
				Connection c=DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/test?serverTimezone=UTC&characterEncoding=UTF-8",
		                "root", "123456");
	 
	            String sql = "insert into order_(uid) values(?)";
	 
	            PreparedStatement ps = c.prepareStatement(sql,Statement.RETURN_GENERATED_KEYS);
	 
	            ps.setInt(1, o.getUser().getId());
	 
	            ps.execute();
	 
	            ResultSet rs = ps.getGeneratedKeys();
	            if (rs.next()) {
	                int id = rs.getInt(1);
	                o.setId(id);
	            }
	 
	            ps.close();
	 
	            c.close();
	 
	        } catch (ClassNotFoundException e) {
	            // TODO Auto-generated catch block
	            e.printStackTrace();
	        } catch (SQLException e) {
	            // TODO Auto-generated catch block
	            e.printStackTrace();
	        }
	 
	    }
	 
	}


	
	public class OrderItemDAO {
	 
		public static void main(String[] args) {
			 
	    }
	     
	    public void insert(OrderItem oi) {
	 
	        try {
	        	
	        	Class.forName("com.mysql.cj.jdbc.Driver");
				
				//数据库连接，本地 3306端口，test数据库，UTC时区，UTF-8编码，用户名root ，密码 123456
				Connection c=DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/test?serverTimezone=UTC&characterEncoding=UTF-8",
		                "root", "123456");
	 
	            String sql = "insert into orderitem values(null,?,?,?)";
	 
	            PreparedStatement ps = c.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS);
	 
	            ps.setInt(1,oi.getProduct().getId());
	            ps.setInt(2,oi.getNum());
	            ps.setInt(3,oi.getOrder().getId());
	             
	            ps.execute();
	 
	            ps.close();
	 
	            c.close();
	 
	        } catch (ClassNotFoundException e) {
	            // TODO Auto-generated catch block
	            e.printStackTrace();
	        } catch (SQLException e) {
	            // TODO Auto-generated catch block
	            e.printStackTrace();
	        }
	         
	    }
	 
	}

## Controller Servlet 部分 
// 用户
public class UserLoginServlet extends HttpServlet {

	@Override
	protected void service(HttpServletRequest request, HttpServletResponse response)

		throws ServletException, IOException {
		// TODO Auto-generated method stub
		//通过request获取post 表单数据
		String name = request.getParameter("name");
		String password = request.getParameter("password");
		//通过dao层从数据库获取用户名密码 ，存入user对象
		User user = new UserDAO().getUser(name, password);
		//user 不为空
		if (null != user) {
			// 获取一个session对象，存入user值
			request.getSession().setAttribute("user", user);

			response.sendRedirect("listProduct");
		//否则重定向
		} else
			response.sendRedirect("login.jsp");

	}
	}

//产品

	public class ProductListServlet extends HttpServlet {

	@Override
	protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		// list 指定类型 product
		List<Product> products=new ProductDAO().ListProduct();
		
		request.setAttribute("products", products);
		// 请求转发 传参
		request.getRequestDispatcher("/listProduct.jsp").forward(request, response);
		
	}

	}

// 购买产品

	public class OrderItemAddServlet extends HttpServlet {
 
    protected void service(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
		//	将request 中的num 与pid 转为int 类型
        int num = Integer.parseInt(request.getParameter("num"));
        int pid = Integer.parseInt(request.getParameter("pid"));
    
        // 新建一个product 对象，执行dao层
        Product p = new ProductDAO().getProduct(pid);
		//新建一个Orderitem 对象
        OrderItem oi = new OrderItem();
		//存入orderitem 对象
        oi.setNum(num);
        oi.setProduct(p);
       
        // 获取session 对象值
        List<OrderItem> ois = (List<OrderItem>) request.getSession().getAttribute("ois");
		//如何ois 未取到值
        if (null == ois) {
			
            ois = new ArrayList<OrderItem>();
            request.getSession().setAttribute("ois", ois);
        }
        
        // 购买相同东西物品
        boolean found = false;
        for (OrderItem orderItem : ois) {
            if (orderItem.getProduct().getId() == oi.getProduct().getId()) {
                orderItem.setNum(orderItem.getNum() + oi.getNum());
                found = true;
                break;
            }
        }
 
        if (!found)
            ois.add(oi);
        
        response.sendRedirect("/Test/listOrderItem");
 
    }
	}

//删除购物车内产品

	public class OrderItemDeleteServlet extends HttpServlet {

	/**
	 * 
	 */
	private static final long serialVersionUID = 1L;

	@Override
	protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// 获取传过来的产品Id
		int pid=Integer.parseInt(request.getParameter("pid"));
		// 获取Session会话中的值取出到
		List<OrderItem> ois=(List<OrderItem>)request.getSession().getAttribute("ois");
		
		List<OrderItem> oisdelete= new ArrayList<>();
		
		if(null!=ois){
			for(OrderItem io:ois){
				if(io.getProduct().getId()==pid){
					oisdelete.add(io);
				}
			}
		}
		
		ois.removeAll(oisdelete);
		
		response.sendRedirect("/Test/listOrderItem");
	}
	
	}
//显示购物车内容

	public class OrderItemListServlet extends HttpServlet
	{
 
    /**
	 * 
	 */
	private static final long serialVersionUID = 1L;

	protected void service(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
    	
    	  //List<OrderItem> ois = (List<OrderItem>) request.getSession().getAttribute("ois");
    	  
    	  request.getRequestDispatcher("listOrderItem.jsp").forward(request, response);
        
    	  
    	  
    	  
       
    }
	}
// 创建订单

	public class OrderCreateServlet extends HttpServlet {
 
    protected void service(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
    		
        User u = (User) request.getSession().getAttribute("user");
        	
        if(null==u){
        	
            response.sendRedirect("login.jsp");
            return;
            
        }
         
        Order o = new Order();
        o.setUser(u);
        
        new OrderDAO().insert(o);
        
        List<OrderItem> ois = (List<OrderItem>) request.getSession().getAttribute("ois");
        for (OrderItem oi : ois) {
            oi.setOrder(o);
            new OrderItemDAO().insert(oi);
        }
         
        ois.clear();
         
        response.setContentType("text/html; charset=UTF-8");
        response.getWriter().println("订单创建成功");
        
    }
	}