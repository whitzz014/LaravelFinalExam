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
#### Delete Function 
```
public  function destroy(Gallery $gallery) {
    $gallery->delete();
    return redirect('galleries');
}
```
#### if you know the id 
```
public  function destroy($gallery) {
    $gallery->destroy($gallery);
    return redirect('galleries');
}
```

```
<form  method="POST" action="{{ route('galleries.destroy', $gallery->id) }}>
    @csrf
    @method('DELETE')
    <input type="submit" value="Delete">
    </form>
    <br>
```
- Update the Controller so only th authenticated can delete
```
class GalleryController extends Controller {
    public function __construct(){
        $this->middleware('auth', [except => ['index','show']]);
    }
}
```
### Soft Deleting Records 
- soft deletes allow you to archive your deleted items for later inspection or recovery 
- soft deletes mark database rows as deleted without deleting them 
- Eloquents soft delete function requires a __deleted_at__ column to be added to the table 
- once you enable soft deletes on that Eloquent Model, every query you write (unless you explicity add soft delete records) will be scoped to ignore softdeleted rows 

- You enable soft deletes by doing three things: adding a __deleted_at__ column in a migration, importing __SoftDeletes__ trait in the model and adding the __deleted_at__ column to your __$dates__ property 

#### Make soft delete Migration 
``` 
php artisan make:migration add_softdeletes_to_galleries --table=galleries 
```

- Theres a __softDeletes()__ method available on the query builder to add the __deleted_at__ column to a table 
```
return new class extends Migration {
    public function up(){
        Schema::table('galleries', function  (Blueprint $table){
            $table->softDeletes(); 
        });
    }

    public function down(){
        Schema::table('galleries', function   (Blueprint $table){
            $table->dropSoftDeletes(); 
        });
    }
}
```

- once you make these changes every __delete()__ and __destroy()__ call will now set the __deleted_at__ column on your row to be the current date and time instead of deleting that row and all future queries will exclude that row as a result 

```
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;
    class Gallery extends Model {
            use HasFactory, SoftDeletes;
            
            protected $dates = ['deleted_at']; //mark this column as a date
            
            ...
    }

```

- you can include the list of soft-deleted items by using the __withTrashed()__ method 
```
    public function index(){
        $galleries = Gallery::withTrashe()->get();
        return view('galleries.index', compact('galleries'));
    }
```
- You can use the __trashed()__ method to determine if an instnace has been soft deleted 
- you can see a list of all soft-deleted items by using the __onlyTrashed()__ method

#### Restore 
```
public function restore($id){
    Gallery::withTrashed()->where('id', $id)->restore();
    return redirect('galleries');
}
```
#### Restore ALL soft deleted
```
Gallery::onlyTrashed()->restore();
```
#### Force Delete
```
public function forceDelete($id){
    Gallery::onlyTrashed()->where('id', $id)->forceDelete();
    return redirect('galleries');
}
```
#### Force Delete ALL soft deleted
```
Gallery::onlyTrashed()->forceDelete();
```