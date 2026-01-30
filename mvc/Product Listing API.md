### controller

using Microsoft.AspNetCore.Mvc;

using Microsoft.EntityFrameworkCore;

using Product.WebAPI.Data;

using Product.WebAPI.Helpers;

using Product.WebAPI.Services;

using static Microsoft.EntityFrameworkCore.DbLoggerCategory;
 
namespace Product.WebAPI.Controllers

{

    [ApiController]

    [Route("api/[controller]")]
 
    public class ProductController : ControllerBase

    {

        private readonly ProductContext _context;

        public ProductController(ProductContext context)

        {

            _context = context;

        }

        [HttpPost]

        public async Task<IActionResult> AddProduct(ProductItem p)

        {

            var add = new ProductItem

            {

                Price = p.Price,

                Name = p.Name,

                Brand = p.Brand,

                Active = p.Active,

                Description = p.Description,

                ImageLink = p.ImageLink

            };

            await _context.ProductItems.AddAsync(add);

            await _context.SaveChangesAsync();

            return Created("", add);

        }
 
        [HttpGet]

        public async Task<IActionResult> GetByquery([FromQuery] Filters f)

        {

            IQueryable<ProductItem> query = _context.ProductItems.Where(x => x.Active == true);

            if(!(string.IsNullOrWhiteSpace(f.Name)))

            {

                query = query.Where(o => o.Name.Contains(f.Name));

            }

            if(!(string.IsNullOrWhiteSpace(f.Brand)))

            {

                query  = query.Where(x=> x.Brand == (f.Brand));

            }

            if(f.PriceFrom.HasValue)

            {

                query = query.Where(x => x.Price >= f.PriceFrom.Value);

            }

            if(f.PriceTo.HasValue) 

            {

                query = query.Where(x=> x.Price <= f.PriceTo.Value);

            }

            if(f.Sort.ToLower() == "price")

            {

                if (f.SortDir.ToLower() == "desc")

                {

                    query = query.OrderByDescending(x=>x.Price);

                }

                else

                {

                    query = query.OrderBy(x => x.Price);

                }

            }

            else

            {

                if(f.SortDir.ToLower()=="desc")

                {

                    query = query.OrderByDescending(x=> x.Name);

                }

                else

                {

                    query = query.OrderBy(x => x.Name);

                }

            }

            var final = await query.ToListAsync();

            return Ok(new { TotalProducts = final.Count , Products = final });

        }
 
        [HttpGet("{id}")]

        public async Task<IActionResult> GetById(int id)

        {

            var query = await _context.ProductItems.FirstOrDefaultAsync(x=>x.Id == id);

            if(query == null)

            {

                return NotFound();

            }

            return Ok(query);

        }

    }

}

 