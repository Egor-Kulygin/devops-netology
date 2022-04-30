Клонироем репозиторий ( git clone https://github.com/hashicorp/terraform.git)
Переходим в клонированный репозиторий (cd terraform)
Проверяем что находимся в нужном репозитории (git remote –v)


1)	git show aefea                                                     
полный хэш - aefead2207ef7e2aa5dc81a34aedf0cad4c32545
комментарий - Update CHANGELOG.md


2)	git show 85024d3 

(находит тег вместо хэша, принудительно искать хэш, команду не нашел)
Или может нужно было решать это по другому?

warning: refname '85024d3' is ambiguous.
commit 488853be9cc549527db3760573be459b2d49a74e (HEAD, tag: 85024d3, origin/main, origin/HEAD, main)
Author: Kevin Wang <kwangsan@gmail.com>
Date:   Fri Apr 29 15:33:45 2022 -0400

(для дальнейшего поиска пришлось удалить тег командой git tag -d 85024d3)
После удаления тега нашел необходимый коммит. Так как изменения были только в локальном репозитории, то можно заново все загрузить. Или поставить тег обратно.

commit 85024d3100126de36331c6982bfaac02cdab9e76 (tag: v0.12.23)
Author: tf-release-bot <terraform@hashicorp.com>
Date:   Thu Mar 5 20:56:10 2020 +0000
v0.12.23
 // Или может нужно было решать это по-другому?


3) git log b8d720 --no-walk  (два родителя)
56cd7859e
9ea88f22f
Или можно по-другому
git log b8d720 --pretty=format:'%h' --graph -5
*   'b8d720f83'
|\
| * '9ea88f22f'
|/
*   '56cd7859e'


4) git log  v0.12.23..v0.12.24  --oneline
33ff1c03b (tag: v0.12.24) v0.12.24
b14b74c49 [Website] vmc provider links
3f235065b Update CHANGELOG.md
6ae64e247 registry: Fix panic when server is unreachable
5c619ca1b website: Remove links to the getting started guide's old location
06275647e Update CHANGELOG.md
d5f9411f5 command: Fix bug when using terraform login on Windows
4b6d06cc5 Update CHANGELOG.md
dd01a3507 Update CHANGELOG.md
225466bc3 Cleanup after v0.12.23 release


5) git log -S "func providerSource" 
(--oneline, не использовал, так как нужно было найти ранний коммит, в сокращённом дата не указывается)
Находим хэш коммита 8c928e83589d90a031f811fae52a81be7153e82f
далее
git show 8c928e83589d90a031f811fae52a81be7153e82f
ctrl + F поиск по «func providerSource»
итог - func providerSource(services *disco.Disco)


6) git grep "func globalPluginDirs" (находим где находится функция)
plugins.go:func globalPluginDirs() []string {
git log -s -L :globalPluginDirs:plugins.go –oneline 
( внутри файла plugins.go  найти изменения в функции globalPluginDirs)
52dbf9483 keep .terraform.d/plugins for discovery
41ab0aef7 Add missing OS_ARCH dir to global plugin paths
66ebff90c move some more plugin search path logic to command
8364383c3 Push plugin discovery down into command package

7) Author: Martin Atkins <mart@degeneration.co.uk>
    git log -SsynchronizedWriters
 
