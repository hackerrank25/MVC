using Microsoft.AspNetCore.Http;

using Microsoft.AspNetCore.Mvc;

using ProductOrderApi.Data;

using ProductOrderApi.Data.Entities;

using ProductOrderApi.Data.Models;

using ProductOrderApi.Services;

using System.Numerics;

using Microsoft.EntityFrameworkCore;
 
namespace ProductOrderApi.Controllers

{

    [Route("api/[controller]")]

    [ApiController]

    public class OrdersController : ControllerBase

    {

        private readonly OrderService _orderService;

        private readonly OrderContext _context;

        public OrdersController(OrderService orderService, OrderContext context)

        {

            _context=context;

            _orderService = orderService;

        }

        [HttpGet]

        public async Task<ActionResult<IEnumerable<Order>>> GetOrders()

        {

            var data =  await _context.Orders.ToListAsync();

            return Ok(data);

        }

        [HttpGet("{id}")]

        public async Task<ActionResult<Order>> GetOrder(int id)

        {

            var data = await _context.Orders.FindAsync(id);

            if(data==null)

            {

                return NotFound();

            }

            return Ok(data);

        }

        [HttpPost]

        public async Task<ActionResult<Order>> AddOrder([FromBody]CreateOrderModel model)

        {

            var data = model.OrderProducts;

            decimal total = 0;

            foreach (var d in data)

            { 

                var product = await _context.Products.SingleAsync(x=>x.Id==d.ProductId);

                int Q = d.Quantity;

                 total=+ d.Quantity * product.Price;

             };

            var order = new Order

            {

                TotalPrice = total,

                OrderDate = DateTime.Now,

            };

            _context.Orders.Add(order);

            await _context.SaveChangesAsync();

            return Ok(order);

        }

        [HttpPut("{id}")]

        public async Task<IActionResult> UpdateOrder(int id, Order order)

        {

            if(id!=order.Id)

            {

                return BadRequest();

            }

            var data = await _context.Orders.FindAsync(id);

            if(data==null)

            {

                return NotFound();

            }

            data.OrderDate = order.OrderDate;

            data.OrderProducts=order.OrderProducts;

            data.TotalPrice=order.TotalPrice;

            await _context.SaveChangesAsync();

            return NoContent();

        }

        [HttpDelete("{id}")]

        public async Task<IActionResult> DeleteOrder(int id)

        {

            var data = await _context.Orders.FindAsync(id);

            if (data == null)

            {

                return NotFound();

            }

            _context.Orders.Remove(data);

            await _context.SaveChangesAsync();

            return NoContent();

        }

    }

}
 
 
using Microsoft.AspNetCore.Http;

using Microsoft.AspNetCore.Mvc;

using ProductOrderApi.Data;

using ProductOrderApi.Data.Entities;

using ProductOrderApi.Data.Repositories;

using ProductOrderApi.Helpers;

using ProductOrderApi.Services;

using SQLitePCL;

using Microsoft.EntityFrameworkCore;

using System.Runtime.InteropServices;
 
namespace ProductOrderApi.Controllers

{

    [Route("api/[controller]")]

    [ApiController]

    public class ProductsController : ControllerBase

    {

        private readonly ProductService _productService;

        private readonly ProductRepository _productRepository;

        private readonly OrderContext _context;
 
 
        public ProductsController(ProductService productService,OrderContext context)

        {

            _context = context;

            _productService = productService;

        }

        [HttpGet]

        public async Task<ActionResult<IEnumerable<Product>>> GetProducts()

        {

            var data = await _context.Products.ToListAsync();

            return Ok(data);
 
        }

        [HttpGet("{id}")]

        public async Task<ActionResult<Product>> GetProduct(int id)

        {

            var data = await _context.Products.FindAsync(id);

            if(data==null)

            {

                return NotFound();

            }

            return Ok(data);

        }

        [HttpPost]

        public async Task<ActionResult<Product>> AddProduct(Product product)

        {

             _context.Products.Add(product);

            await _context.SaveChangesAsync();

            return Ok( product);

        }

        [HttpPut("{id}")]

        public async Task<IActionResult> UpdateProduct(int id, Product product)

        {

            if(id!=product.Id)

            {

                return BadRequest();

            }

            var data= await _context.Products.FindAsync(id);

            if(data==null)

            {

                return NotFound();

            }

            data.Price = product.Price;

            data.Name=product.Name;

            await _context.SaveChangesAsync();

            return NoContent();

        }

        [HttpDelete("{id}")]

        public async Task<IActionResult> DeleteProduct(int id)

        {

            var data = await _context.Products.FindAsync(id);

            if (data == null)

            {

                return NotFound();

            }

            _context.Products.Remove(data);

            return NoContent();

        }       

    }

}
 
 
 