using Bitirmee.Web.Data;
using Bitirmee.Web.Models;
using Microsoft.AspNetCore.Mvc;

namespace Bitirmee.Web.Controllers
{
    public class LoginController : Controller
    {
        private readonly BlogDBContext _context;

        public LoginController(BlogDBContext context)
        {
            _context = context;
        }

        [HttpGet]
        public IActionResult Login()
        {
            var model = new LoginViewModel();
            return View(model);
        }
        [HttpPost]
        public IActionResult Login(LoginViewModel model)
        {
            if (ModelState.IsValid)
            {
                var user = _context.Admins.SingleOrDefault(u => u.UserName == model.UserName);
                if (user != null && user.Password == model.Password)
                {
                    HttpContext.Session.SetString("Username", user.UserName);
                    return RedirectToAction("Index", "Home");
                }
                else
                {
                    ModelState.AddModelError("", "Kullanıcı adı veya Parola geçersiz.");
                }
            }
            return View(model);
        }
        public IActionResult Logout()
        {
            HttpContext.Session.Clear();
            return RedirectToAction("Login", "Login");
        }
    }
}
