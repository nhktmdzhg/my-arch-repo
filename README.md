# 📦 Personal Arch Linux Repository Builder

[🇻🇳 Xem bản tiếng Việt](#-hướng-dẫn-tiếng-việt)

A fully automated, zero-cost Arch Linux binary repository powered entirely by GitHub Actions and GitHub Releases.

This template allows you to maintain a personal `pacman` repository without paying for a VPS or managing a build server. It automatically tracks updates from the Arch User Repository (AUR), builds the packages (including split packages), caches them smartly to save runner memory, and publishes the `.pkg.tar.zst` binaries to a fixed GitHub Release.

---

## 🚀 How to use this repository (For End-Users)

If you just want to install packages that have already been built in this repository, you simply need to add it to your pacman configuration.

### 1. Check CPU compatibility

This repository currently builds packages inside the `cachyos/cachyos-v3` Docker container.

That means the published binaries are intended for systems that support the `x86-64-v3` instruction set or newer.

Before using this repository, make sure your machine supports `x86-64-v3` or higher. If your CPU only supports generic `x86-64`, the packages from this repository may not run correctly on your system.

### 2. Edit your `pacman.conf`

Append the following configuration to the bottom of your `/etc/pacman.conf`:

```ini
[nanoka]
SigLevel = Optional TrustAll
Server = https://github.com/nhktmdzhg/my-arch-repo/releases/download/repository
```

### 3. Sync and Install

Refresh your package databases and install your desired packages just like official Arch packages:

```bash
sudo pacman -Sy
sudo pacman -S <package-name>
```

## 🛠️ Create Your Own Automated Repo

Want to build your own automated AUR farm? Follow these steps to fork and configure this system.

### Step 1: Fork and Clean

- Fork this repository to your own GitHub account.

### Step 2: Choose the correct Docker container

This repository currently uses the `cachyos/cachyos-v3` Docker container in `build-release.yml`.

If you fork this repository, choose the container that matches your target machines:

- `cachyos/cachyos` for generic builds.
- `cachyos/cachyos-v3` for `x86-64-v3` builds.
- `cachyos/cachyos-v4` for `x86-64-v4` builds.

If you need a different target level, edit the workflow file `build-release.yml` and replace the container image accordingly.

### Step 3: Choose the correct makepkg profile

Besides the container choice, the workflows also download a CachyOS `makepkg.conf` profile.

CachyOS makepkg profiles include:

- `docker-makepkg` for generic builds.
- `docker-makepkg-v3` for `x86-64-v3` builds.
- `docker-makepkg-v4` for `x86-64-v4` builds.
- `docker-makepkg-znver4` for AMD Zen 4 optimized builds.

In practice, if you want to customize your own fork, review both the container image and the downloaded makepkg profile so they match your intended CPU target.

### Step 4: Local packages are supported

This repository also supports local packages that are not synced from the AUR.

In `packages.txt`, use the prefix `local:` for packages that you maintain yourself inside this repository. These entries are skipped by the AUR sync workflow, so you can manually keep and update their `PKGBUILD` files in your repo.

Example:

```txt
paru-bin
local:my-custom-package
```

In this example:

- `paru-bin` will be synced from the AUR.
- `my-custom-package` will be treated as a local package and must be maintained manually by the repository owner.

### Step 5: Configure Repository Permissions

To allow GitHub Actions to build and release packages, you must enable specific workflow permissions:

- Go to your repository Settings > Actions > General.
- Under Workflow permissions, select Read and write permissions.
- Check the box that says Allow GitHub Actions to create and approve pull requests.
- Click Save.

### Step 6: Manage Your Packages

The entire repository is controlled by a single file: `packages.txt`.

- Open `packages.txt`.
- Add the exact names of the AUR packages you want to build (one per line).
- For packages that you maintain manually in this repository, use the `local:` prefix.

  > Note on Split Packages: If a package has multiple sub-packages, just write the base package name. The workflow will automatically discover and build all sub-packages.

- Commit the changes to the `main` branch.

### Step 7: Relax and Let it Run

- The Sync Workflow runs on a schedule and when `packages.txt` changes. It reads `packages.txt`, pulls the latest PKGBUILDs from the AUR, skips `local:` entries, deletes removed packages, and creates a clean Pull Request.
- The Publish Workflow runs on schedule, on push to `main`, or manually. It checks existing assets, reuses cached packages when possible, falls back to previous release files if needed, generates a fresh pacman database, and uploads everything to the repository Release tag.

**Disclaimer:** `SigLevel = Optional TrustAll` is used for convenience in this personal setup, meaning packages are not GPG-signed. Only use this configuration for repositories you fully control or trust.

---

## 🇻🇳 Hướng dẫn tiếng Việt

Kho gói nhị phân Arch Linux cá nhân này được tự động hóa hoàn toàn, không tốn chi phí máy chủ riêng, và hoạt động bằng GitHub Actions kết hợp GitHub Releases.

Template này cho phép bạn duy trì một kho `pacman` cá nhân mà không cần thuê VPS hoặc tự quản lý build server. Hệ thống sẽ tự theo dõi cập nhật từ AUR, build gói (bao gồm cả split packages), dùng cache hợp lý để tiết kiệm tài nguyên runner, rồi phát hành file `.pkg.tar.zst` lên một GitHub Release cố định.

### 🚀 Cách dùng repository này (dành cho người dùng cuối)

Nếu bạn chỉ muốn cài các gói đã được build sẵn từ repository này, bạn chỉ cần thêm repo vào cấu hình `pacman`.

#### 1. Kiểm tra khả năng tương thích CPU

Repository này hiện build package trực tiếp bên trong Docker container `cachyos/cachyos-v3`.

Điều đó có nghĩa là các gói nhị phân được phát hành được nhắm tới các máy hỗ trợ tập lệnh `x86-64-v3` trở lên.

Trước khi dùng repository này, hãy chắc chắn rằng máy của bạn hỗ trợ `x86-64-v3` hoặc cao hơn. Nếu CPU của bạn chỉ hỗ trợ mức `x86-64` generic, các gói từ repository này có thể không chạy đúng trên hệ thống của bạn.

#### 2. Chỉnh sửa `pacman.conf`

Thêm đoạn cấu hình sau vào cuối file `/etc/pacman.conf`:

```ini
[nanoka]
SigLevel = Optional TrustAll
Server = https://github.com/nhktmdzhg/my-arch-repo/releases/download/repository
```

#### 3. Đồng bộ và cài đặt

Làm mới cơ sở dữ liệu gói rồi cài gói bạn muốn giống như gói chính thức của Arch:

```bash
sudo pacman -Sy
sudo pacman -S <package-name>
```

### 🛠️ Tạo repository tự động của riêng bạn

Nếu bạn muốn tự dựng một hệ thống build AUR tự động cho tài khoản của mình, hãy làm theo các bước sau.

#### Bước 1: Fork và dọn dẹp

- Fork repository này về tài khoản GitHub của bạn.

#### Bước 2: Chọn Docker container phù hợp

Repository này hiện đang dùng Docker container `cachyos/cachyos-v3` trong workflow `build-release.yml`.

Nếu bạn fork repository này, hãy chọn container phù hợp với máy đích của bạn:

- `cachyos/cachyos` cho build generic.
- `cachyos/cachyos-v3` cho build `x86-64-v3`.
- `cachyos/cachyos-v4` cho build `x86-64-v4`.

Nếu bạn muốn đổi target, hãy sửa file workflow `build-release.yml`, rồi thay container image tương ứng.

#### Bước 3: Chọn makepkg profile phù hợp

Ngoài việc chọn container, workflow còn tải một profile `makepkg.conf` của CachyOS.

Các profile makepkg của CachyOS gồm có:

- `docker-makepkg` cho build generic.
- `docker-makepkg-v3` cho build `x86-64-v3`.
- `docker-makepkg-v4` cho build `x86-64-v4`.
- `docker-makepkg-znver4` cho build tối ưu cho AMD Zen 4.

Trên thực tế, nếu bạn muốn tùy biến fork của mình, hãy kiểm tra cả container image lẫn makepkg profile được tải về để chúng khớp với CPU target bạn muốn.

#### Bước 4: Hỗ trợ gói local không sync từ AUR

Repository này cũng hỗ trợ các gói local không cần đồng bộ từ AUR.

Trong `packages.txt`, hãy dùng tiền tố `local:` cho các gói mà bạn tự maintain trong repository này. Các dòng đó sẽ bị bỏ qua trong workflow sync AUR, nên bạn có thể tự quản lý và tự cập nhật `PKGBUILD` của chúng trong repo.

Ví dụ:

```txt
paru-bin
local:my-custom-package
```

Trong ví dụ trên:

- `paru-bin` sẽ được sync từ AUR.
- `my-custom-package` sẽ được xem là gói local và chủ repository phải tự maintain thủ công.

#### Bước 5: Cấu hình quyền cho repository

Để GitHub Actions có thể build và phát hành gói, bạn cần bật đúng quyền cho workflow:

- Vào `Settings > Actions > General` của repository.
- Trong phần **Workflow permissions**, chọn **Read and write permissions**.
- Tích vào ô **Allow GitHub Actions to create and approve pull requests**.
- Nhấn **Save**.

#### Bước 6: Quản lý danh sách gói

Toàn bộ repository này được điều khiển bởi một file duy nhất là `packages.txt`.

- Mở file `packages.txt`.
- Thêm đúng tên các gói AUR mà bạn muốn build, mỗi dòng một gói.
- Với các gói bạn tự maintain trong repo, hãy dùng tiền tố `local:`.

  > Lưu ý về Split Packages: Nếu một gói có nhiều gói con, bạn chỉ cần ghi tên gói gốc. Workflow sẽ tự phát hiện và build toàn bộ các gói con liên quan.

- Commit thay đổi vào branch `main`.

#### Bước 7: Để hệ thống tự chạy

- **Sync Workflow** chạy theo lịch và khi `packages.txt` thay đổi. Workflow này đọc `packages.txt`, kéo PKGBUILD mới nhất từ AUR, bỏ qua các dòng `local:`, xóa các gói đã bị loại bỏ, rồi tạo một Pull Request sạch.
- **Publish Workflow** chạy theo lịch, khi push vào `main`, hoặc chạy tay. Workflow này kiểm tra asset hiện có, tận dụng cache nếu có thể, fallback về file từ release trước nếu cần, tạo lại pacman database mới, rồi upload toàn bộ lên Release tag của repository.

**Lưu ý:** `SigLevel = Optional TrustAll` được dùng để tiện cho mô hình repository cá nhân này, nghĩa là gói không được ký GPG. Chỉ nên dùng cấu hình này cho repository do chính bạn kiểm soát hoặc bạn thực sự tin tưởng.
