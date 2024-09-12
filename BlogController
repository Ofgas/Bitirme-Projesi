using Bitirmee.Web.Data;
using Bitirmee.Web.Models.Domain;
using Bitirmee.Web.Models;
using Microsoft.AspNetCore.Mvc;
using System.Linq;

namespace Bitirmee.Web.Controllers
{
    public class BlogController : Controller
    {
        private readonly BlogDBContext _context;

        public BlogController(BlogDBContext context)
        {
            _context = context;
        }

        public ActionResult Index()
        {
            var blogPosts = _context.BlogPosts.ToList();
            return View(blogPosts);
        }

        public ActionResult Add()
        {
            return View(new BlogPosts());
        }

        [HttpPost]
        public ActionResult Add(BlogPosts blogPost)
        {
            bool blogExists = _context.BlogPosts.Any(b => b.Heading == blogPost.Heading);

            if (blogExists)
            {
                TempData["ErrorMessage"] = "Bu başlıklı bir yazı zaten mevcut.";
                return RedirectToAction("Add", "Blog");
            }

            try
            {
                _context.BlogPosts.Add(blogPost);
                _context.SaveChanges();
                TempData["SuccessMessage"] = "Başarıyla Blog Eklendi!";
                return RedirectToAction("Success", "Blog");
            }
            catch (Exception ex)
            {
                TempData["ErrorMessage3"] = "Blog'u kaydederken sorun yaşadık: " + ex.Message;
            }

            return View(blogPost);
        }
        [HttpGet]
        public ActionResult SelectBlog()
        {
            var blogs = _context.BlogPosts.ToList();
            var viewModel = new BlogPostViewModel
            {
                BlogPosts = blogs
            };
            return View(viewModel);
        }

        [HttpPost]
        public ActionResult SelectBlog(BlogPostViewModel model, string action)
        {
            if (model.Id == 0)
            {
                TempData["ErrorMessage2"] = "Blog bulunamadı.";
                model.BlogPosts = _context.BlogPosts.ToList();
                return View(model);
            }

            if (action == "Edit")
            {
                return RedirectToAction("Edit", new { id = model.Id });
            }
            else if (action == "Delete")
            {
                return RedirectToAction("Delete", new { id = model.Id });
            }
            else if (action == "AssignTags")
            {
                return RedirectToAction("AssignTags", new { id = model.Id });
            }

            TempData["ErrorMessage4"] = "Geçersiz aksiyon.";

            model.BlogPosts = _context.BlogPosts.ToList();
            return View(model);
        }

        public ActionResult Edit(int id)
        {
            var blogPost = _context.BlogPosts.Find(id);

            if (blogPost == null)
            {
                TempData["ErrorMessage2"] = "Blog Bulunamadı.";
                return RedirectToAction("Index");
            }

            return View(blogPost);
        }

        [HttpPost]
        public ActionResult Edit(BlogPosts blogPost)
        {
            var edit = _context.BlogPosts.Find(blogPost.Id);

            if (edit == null)
            {
                TempData["ErrorMessage2"] = "Blog bulunamadı.";
                return RedirectToAction("Edit", new { id = blogPost.Id });
            }

            edit.Heading = blogPost.Heading;
            edit.PageTitle = blogPost.PageTitle;
            edit.Content = blogPost.Content;
            edit.ShortDescription = blogPost.ShortDescription;
            edit.UrlHandle = blogPost.UrlHandle;
            edit.PublishedDate = blogPost.PublishedDate;
            edit.Author = blogPost.Author;
            edit.Visible = blogPost.Visible;
            _context.SaveChanges();

            TempData["SuccessMessage2"] = "Blog başarıyla düzenlendi!";
            return RedirectToAction("Success", "Blog");
        }

        public ActionResult Delete(int id)
        {
            var blogPost = _context.BlogPosts.Find(id);

            if (blogPost == null)
            {
                TempData["ErrorMessage2"] = "Blog bulunamadı.";
                return RedirectToAction("Index", "Home");
            }

            _context.BlogPosts.Remove(blogPost);
            _context.SaveChanges();

            TempData["SuccessMessage3"] = "Blog başarıyla silindi!";
            return RedirectToAction("Success", "Blog");
        }
        [HttpGet]
        public ActionResult AssignTags(int? id)
        {
            var blogs = _context.BlogPosts.ToList();
            var tags = _context.Tags.ToList();

            var viewModel = new AssignTagsViewModel
            {
                BlogPostId = id ?? 0,
                BlogPosts = blogs,     
                Tags = tags,
                SelectTagIds = id.HasValue ? _context.BlogPostTags.Where(bpt => bpt.BlogPostId == id).Select(bpt => bpt.TagId).ToList() : new List<int>()
            };

            return View(viewModel);
        }
        [HttpPost]
        public ActionResult AssignTags(AssignTagsViewModel model)
        {
            var blogPost = _context.BlogPosts.Find(model.BlogPostId);
            if (blogPost == null)
            {
                TempData["ErrorMessage2"] = "Blog bulunamadı.";
                return RedirectToAction("AssignTags");
            }

            var existingTagIds = _context.BlogPostTags
                .Where(bpt => bpt.BlogPostId == model.BlogPostId)
                .Select(bpt => bpt.TagId)
                .ToList();

            var newTagIds = model.SelectTagIds.Except(existingTagIds).ToList();

            if (newTagIds.Any())
            {
                foreach (var tagId in newTagIds)
                {
                    var blogPostTag = new BlogPostTag
                    {
                        BlogPostId = model.BlogPostId,
                        TagId = tagId
                    };
                    _context.BlogPostTags.Add(blogPostTag);
                }

                _context.SaveChanges();
                TempData["SuccessMessage4"] = "Yeni etiket atandı!";
            }
            else
            {
                TempData["ErrorMessage4"] = "Etiket Seçilmedi.";
            }

            return RedirectToAction("Success", "Blog");
        }
        public ActionResult Success()
        {
            return View();
        }
    }
}
