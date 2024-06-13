# Simple-To-do-Project

//paketler
import Map "mo:base/HashMap";
import Hash "mo:base/Hash";
import Nat "mo:base/Nat";
import Iter "mo:base/Iter";
import Text "mo:base/Text";

//actor->canister
actor Asistan {

  //type->class
type ToDo={
    description:Text;
    completed:Bool;
};

 
//fonksiyon =>  1)private function 2)public function
func natHash (n:Nat):Hash.Hash{
  Text.hash(Nat.toText(n))
};

//değişkenler =>=>    var =>mutable , let=>imutable
var todos = Map.HashMap<Nat, ToDo>(0, Nat.equal, natHash);
var nextId:Nat=0;


//1)query,2)update
public query func getTodos() :async[ToDo]{
  Iter.toArray(todos.vals());
};

public func addToDo(description : Text) : async Nat{
  let id =nextId;
  todos.put(id,{description =description;completed=false});
  nextId+=1;
  id// return id;
};
 public func completeTodo(id : Nat) : async () {
    ignore do ? {
      let description = todos.get(id)!.description;
      todos.put(id, { description; completed = true });//daha önceden bu id var mı
    }
  };

  public query func showTodos() : async Text {
    var output : Text = "\n___TO-DOs___";
    for (todo : ToDo in todos.vals()) {
      output #= "\n" # todo.description;//text olarak yazma
      if (todo.completed) { output #= " !"; };
    };
    output # "\n"//return 
  };

  public func clearCompleted() : async () {
    todos := Map.mapFilter<Nat, ToDo, ToDo>(todos, Nat.equal, natHash, 
              func(_, todo) { if (todo.completed) null else ?todo });
  };
}
