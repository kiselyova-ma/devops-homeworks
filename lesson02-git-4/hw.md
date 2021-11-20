# Домашнее задание к занятию «2.4. Инструменты Git»

1.Найдите полный хеш и комментарий коммита, хеш которого начинается на `aefea`.
```
PS C:\Users\marii\PycharmProjects\terraform> git show aefea --pretty=format:"%H" --abbrev-commit
aefead2207ef7e2aa5dc81a34aedf0cad4c32545
diff --git a/CHANGELOG.md b/CHANGELOG.md
index 86d70e3e0..588d807b1 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -27,6 +27,7 @@ BUG FIXES:
 * backend/s3: Prefer AWS shared configuration over EC2 metadata credentials by default ([#25134](https://github.com/hashicorp/terraform/issues/25134))
 * backend/s3: Prefer ECS credentials over EC2 metadata credentials by default ([#25134](https://github.com/hashicorp/terraform/issues/25134))
 * backend/s3: Remove hardcoded AWS Provider messaging ([#25134](https://github.com/hashicorp/terraform/issues/25134))
+* command: Fix bug with global `-v`/`-version`/`--version` flags introduced in 0.13.0beta2 [GH-25277]
 * command/0.13upgrade: Fix `0.13upgrade` usage help text to include options ([#25127](https://github.com/hashicorp/terraform/issues/25127))
 * command/0.13upgrade: Do not add source for builtin provider ([#25215](https://github.com/hashicorp/terraform/issues/25215))
 * command/apply: Fix bug which caused Terraform to silently exit on Windows when using absolute plan path ([#25233](https://github.com/hashicorp/terraform/issues/25233))
```
2.Какому тегу соответствует коммит `85024d3`?
```
PS C:\Users\marii\PycharmProjects\terraform> git describe 85024d3
v0.12.23
```

3.Сколько родителей у коммита `b8d720`? Напишите их хеши.
- первые родители
`commit 56cd7859e05c36c06b56d013b55a252d0bb7e158`
```
PS C:\Users\marii\PycharmProjects\terraform> git show b8d720^
commit 56cd7859e05c36c06b56d013b55a252d0bb7e158
Merge: 58dcac4b7 ffbcf5581
Author: Chris Griggs <cgriggs@hashicorp.com>
Date:   Mon Jan 13 13:19:09 2020 -0800
```
- если нужны все родители и родители родители, то
`commit 56cd7859e05c36c06b56d013b55a252d0bb7e158`
`commit 9ea88f22fc6269854151c571162c5bcf958bee2b`
```
PS C:\Users\marii\PycharmProjects\terraform> git show b8d720^@
commit 56cd7859e05c36c06b56d013b55a252d0bb7e158
Merge: 58dcac4b7 ffbcf5581
Author: Chris Griggs <cgriggs@hashicorp.com>
Date:   Mon Jan 13 13:19:09 2020 -0800

commit 9ea88f22fc6269854151c571162c5bcf958bee2b
Author: Chris Griggs <cgriggs@hashicorp.com>
Date:   Tue Jan 21 17:08:06 2020 -0800
```

4.Перечислите хеши и комментарии всех коммитов которые были сделаны между тегами  v0.12.23 и v0.12.24. \

Комментариев не оказалось
```
PS C:\Users\marii\PycharmProjects\terraform> git log v0.12.23..v0.12.24 --pretty=format:"%H" --abbrev-commit
33ff1c03bb960b332be3af2e333462dde88b279e
b14b74c4939dcab573326f4e3ee2a62e23e12f89
3f235065b9347a758efadc92295b540ee0a5e26e
6ae64e247b332925b872447e9ce869657281c2bf
5c619ca1baf2e21a155fcdb4c264cc9e24a2a353
06275647e2b53d97d4f0a19a0fec11f6d69820b5
d5f9411f5108260320064349b757f55c09bc4b80
4b6d06cc5dcb78af637bbb19c198faff37a066ed
dd01a35078f040ca984cdd349f18d0b67e486c35
225466bc3e5f35baa5d07197bbc079345b77525e
```
Зато есть сабж и бади :D
```
PS C:\Users\marii\PycharmProjects\terraform> git log v0.12.23..v0.12.24 --pretty=format:"%H %s %b"
33ff1c03bb960b332be3af2e333462dde88b279e v0.12.24
b14b74c4939dcab573326f4e3ee2a62e23e12f89 [Website] vmc provider links
3f235065b9347a758efadc92295b540ee0a5e26e Update CHANGELOG.md
6ae64e247b332925b872447e9ce869657281c2bf registry: Fix panic when server is unreachable Non-HTTP errors previously resulted in a panic due to dereferencing the
resp pointer while it was nil, as part of rendering the error message.
This commit changes the error message formatting to cope with a nil
response, and extends test coverage.

Fixes #24384

5c619ca1baf2e21a155fcdb4c264cc9e24a2a353 website: Remove links to the getting started guide's old location Since these links were in the soon-to-be-deprecated 0.11 language section, I
think we can just remove them without needing to find an equivalent link.

06275647e2b53d97d4f0a19a0fec11f6d69820b5 Update CHANGELOG.md
d5f9411f5108260320064349b757f55c09bc4b80 command: Fix bug when using terraform login on Windows
4b6d06cc5dcb78af637bbb19c198faff37a066ed Update CHANGELOG.md
dd01a35078f040ca984cdd349f18d0b67e486c35 Update CHANGELOG.md
225466bc3e5f35baa5d07197bbc079345b77525e Cleanup after v0.12.23 release
```

5.Найдите коммит в котором была создана функция `func providerSource`, ее определение в коде выглядит 
так `func providerSource(...)` (вместо троеточего перечислены аргументы).
Коммит - 8c928e835
Фуекция, сортировка по дате, первая дата и есть дата создания. 
```
PS C:\Users\marii\PycharmProjects\terraform> git log -G'func providerSource' --pretty=format:"%ad %h" --author-date-order --reverse
Thu Apr 2 18:04:39 2020 -0700 8c928e835
Tue Apr 21 16:28:59 2020 -0700 5af1e6234
Wed Apr 22 16:28:06 2020 -0700 f5012c12d
```
Чтобы удостовериться, можно посмотреть вывод по `git show 8c928e835`

6.Найдите все коммиты в которых была изменена функция `globalPluginDirs`.
Вот они:
`78b12205587fe839f10d946ea3fdc06719decb05`
`52dbf94834cb970b510f2fba853a5b49ad9b1a46`
`41ab0aef7a0fe030e84018973a64135b11abcd70`
`66ebff90cdfaa6938f26f908c7ebad8d547fea17`
`8364383c359a6b738a436d1b7745ccdce178df47`

- находим файл
```
PS C:\Users\marii\PycharmProjects\terraform> git grep -p "func globalPluginDirs()"
plugins.go=import (
plugins.go:func globalPluginDirs() []string {
```
- находим коммиты
```
PS C:\Users\marii\PycharmProjects\terraform> git log --pretty=format:"%C(bold blue)%H %C(bold blue)%ad" --author-date-order -L :globalPluginDirs:plugins.go
```

7.Кто автор функции `synchronizedWriters`? 
Автор - Martin Atkins 
```
PS C:\Users\marii\PycharmProjects\terraform> git log -G'func synchronizedWriters' --pretty=format:"%ad %h %an" --author-date-order --reverse
Wed May 3 16:25:41 2017 -0700 5ac311e2a Martin Atkins
Mon Nov 30 18:02:04 2020 -0500 bdfea50cc James Bardin
```