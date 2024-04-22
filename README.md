# LARAVEL FINAL EXAM 
## WEEK 9 

### Actions Handled By Resource Contoller
<img src="actionHandle-1.png" alt="Action Handling Image" width="500">

### Deleting Records 
- Delete records in laravel is similar to editing one 
- As per __REST__ principles, a delete action requires a delete method type 
- the delete method is named __destroy__

#### Route::delete
```
Route::delete('galleries/{gallery}', [GalleryController::class, 'destroy'])->name('galleries.destroy');
```
```
public  function destroy(Gallery $gallery) {
    $gallery->delete();
    return redirect('galleries');
}
```

