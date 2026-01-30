### controller 

using Microsoft.AspNetCore.Mvc;
using RestaurantCollection.WebApi.DataAccess;
using RestaurantCollection.WebApi.DTO.Forms;
using RestaurantCollection.WebApi.Models;

namespace RestaurantCollection.WebApi.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class RestaurantController : ControllerBase
    {
        private readonly IRepository _repository;
        // private readonly RestaurantCollectionContext _context;
        public RestaurantController(IRepository repository)
        {
            _repository = repository;
        }

        [HttpPost]
        public async Task<IActionResult> AddRestro(Restaurant restaurant)
        {
            var x  = await _repository.AddRestaurant(restaurant);
            return Created("",x);
        }

       [HttpPut("{id}")] 
       public async Task<IActionResult> UpdateRestro(int id, UpdateForm updateForm)
       {
         var x = await _repository.UpdateRestaurant(id,updateForm);
         return NoContent();
       }

       [HttpGet]
       public async Task<IActionResult> GetAll()
       {
         var x =  await _repository.GetRestaurants();
         return Ok(x); 
       }

       [HttpGet("query")]
       public async Task<IActionResult> GetByQuery([FromQuery] RestaurantQueryModel query)
       {
        var x = await _repository.GetRestaurants(query);
         return Ok(x);
       }

       [HttpDelete("{id}")]
       public async Task<IActionResult> DeleteRestro(int id)
       {
        var x =  await _repository.DeleteRestaurant(id);
        return NoContent();
       }

       [HttpGet("sort")]
       public async Task<IActionResult> SortedData()
       {
        var x = await _repository.GetRestaurantsSorted();
        return Ok(x);
       }
    }
}