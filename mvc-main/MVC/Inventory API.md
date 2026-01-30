### controller

using InventoryWebApi.DataAccess;
using Microsoft.AspNetCore.Mvc;
using InventoryWebApi.Models;
using Microsoft.EntityFrameworkCore;

namespace InventoryWebApi.Controllers
{
    [ApiController]
    [Route("inventory/item")]
    public class InventoryController : ControllerBase
    {
        private readonly IRepository _repository;
        private readonly InventoryContext _context;

        public InventoryController(IRepository repo,InventoryContext context)
        {
            _context=context;
            _repository=repo;
        }

        [HttpPost]
        public async Task<IActionResult> AddItem(InventoryItem inventoryItem)
        {
            var x = await _repository.AddInventoryItem(inventoryItem);
            return Created("",x);
        }

        [HttpDelete("{Barcode}")]
        public async Task<IActionResult> DeleteItem(int Barcode)
        {
            var y = await _context.Inventory.FirstOrDefaultAsync(x=> x.Barcode == Barcode);
            if(y == null)
            {
                return Ok();
            }
            await _repository.DeleteInventoryItem(y); 
            return NoContent();
        }

        [HttpGet("query")]
        public async Task<IActionResult> GetFromQuery([FromQuery] ItemQueryModel query)
        {
            var x  = _repository.GetInventoryItem(query);
            return Ok(x);
        }

        [HttpGet]
        public async Task<IActionResult> GetAll()
        {
            var items = await _repository.GetInventory();
            return Ok(items);
        }

        [HttpGet("sort")]
        public async Task<IActionResult> SortedData()
        {
            var x = await _repository.GetSorted();
            return Ok(x);
        }

        [HttpPut("{barcode}")]
        public async Task<IActionResult> UpdateData(InventoryItem inventoryItem)
        {
            var x =  await _repository.UpdateInventoryItem(inventoryItem);
            if( x == null)
            {
                return NotFound();
            }
            return NoContent();
        }
    }
}
