公式サイト - https://vlang.io/

# 3行で

2019年6月に公開予定のSimple, fast, safeをウリにした言語。
現在は[Web上でPlaygroundで遊べたり](https://vlang.io/play)、[月30ドル払うとアーリーアクセスでコンパイラが落とせたり](https://www.patreon.com/vlang)する。
ビルドが早い、nullもundefinedも無い。最高〜

# 特徴

- Fast compilation
  - doom3のコードが0.5秒でビルド出来るらしい、果たしてcなら何秒なんだ
  - https://vlang.io/compilation_speed
- No global state
- No null
- No undefined values
- Option types
- Generics
  - アーリーアクセスのコンパイラには実装されていない、結局これ使ったらコンパイル時間伸びたりしないのだろうか・・・
- Immutability by default
- Partially pure functions

# サンプルコード

```golang
struct User {
	id int 
} 

struct Repo {
	users []User 
} 

fn new_repo() Repo {
        user := User{id:10}
        return Repo {
                users: [user]
        }
} 

fn (r Repo) find_user_by_id(id int) User? { 
	for user in r.users {
		if user.id == id {
			// V automatically wraps this into an option type  
			return user 
		} 
	} 
	return error('User $id not found') 
} 

fn main() {
	repo := new_repo() 
	// Option types must be handled by `or` blocks  
	user := repo.find_user_by_id(10) or { 
		// `or` block must end with `return`, `break`, or `continue`  
		println('not found') 
		return  
	} 
	println(user.id) // ==> '10'  
}  
```

パッと見Goっぽい。
デフォルトがImmutabilityだったり、Optional返すときは必ずorブロックでどうにかしないといけなかったりする点がとても好き。
コンパイル速度の速さを生かしてゲーム開発に役立てたい。
