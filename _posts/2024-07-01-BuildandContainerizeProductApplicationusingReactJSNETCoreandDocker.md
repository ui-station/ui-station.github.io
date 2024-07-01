---
title: "React JS, NET Core, Docker를 사용해 제품 애플리케이션 구축 및 컨테이너화하는 방법"
description: ""
coverImage: "/assets/img/2024-07-01-BuildandContainerizeProductApplicationusingReactJSNETCoreandDocker_0.png"
date: 2024-07-01 16:17
ogImage: 
  url: /assets/img/2024-07-01-BuildandContainerizeProductApplicationusingReactJSNETCoreandDocker_0.png
tag: Tech
originalTitle: "Build and Containerize Product Application using React JS, .NET Core, and Docker"
link: "https://medium.com/@jaydeepvpatil225/build-and-containerize-product-application-using-react-js-net-core-and-docker-dab8414d11c0"
---



![이미지](/assets/img/2024-07-01-BuildandContainerizeProductApplicationusingReactJSNETCoreandDocker_0.png)

# 소개

본 글에서는 .NET Core Web API를 사용하여 샘플 제품 애플리케이션의 백엔드를 만들고 React JS를 사용하여 웹 폼을 생성하는 방법을 살펴볼 것입니다. 또한 도커를 활용해 동일한 애플리케이션을 컨테이너화할 것입니다.

# 안내


<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

- 샘플 제품 애플리케이션: 백엔드 (.NET Core Web API)
- 샘플 제품 애플리케이션: 프론트엔드 (React JS)
- 애플리케이션을 위한 Docker 파일
- 애플리케이션을 컨테이너화하기

## 필수 사항

- Visual Studio 2022
- Docker Desktop
- NPM
- .NET Core SDK
- React JS

# 샘플 제품 애플리케이션: 백엔드 (.NET Core Web API)

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

1. 새로운 제품 관리 .NET Core 웹 API를 생성해주세요.

2. 데이터베이스 마이그레이션 및 SQL Server와의 연결을 위해 사용한 다음 NuGet 패키지를 설치해주세요.

[이미지](/assets/img/2024-07-01-BuildandContainerizeProductApplicationusingReactJSNETCoreandDocker_1.png)

3. 엔티티 폴더 안에 제품 클래스를 추가해주세요.

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>


```js
namespace ProductManagementAPI.Entities
{
    public class Product
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public decimal Price { get; set; }
    }
}
```

Step 4. Create an AppDbContext class inside the data folder with a SQL Server connection and a DB set property.

```js
using Microsoft.EntityFrameworkCore;
using ProductManagementAPI.Entities;

namespace ProductManagementAPI.Data
{
    public class AppDbContext : DbContext
    {
        public DbSet<Product> Products { get; set; }

        protected readonly IConfiguration Configuration;

        public AppDbContext(IConfiguration configuration)
        {
            Configuration = configuration;
        }
        protected override void OnConfiguring(DbContextOptionsBuilder options)
        {
            options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
            options.UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
        }
    }
}
```

Step 5. Add a product repository inside the repositories folder.


<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

IProductRepository

```js
using ProductManagementAPI.Entities;

namespace ProductManagementAPI.Repositories
{
    public interface IProductRepository
    {
        void AddProduct(Product product);
        void DeleteProduct(int id);
        List<Product> GetAllProducts();
        Product GetProductById(int id);
        void UpdateProduct(Product product);
    }
}
```

ProductRepository

```js
using Microsoft.EntityFrameworkCore;
using ProductManagementAPI.Data;
using ProductManagementAPI.Entities;

namespace ProductManagementAPI.Repositories
{
    public class ProductRepository : IProductRepository
    {
        private readonly AppDbContext _context;

        public ProductRepository(AppDbContext context)
        {
            _context = context;
        }

        public List<Product> GetAllProducts()
        {
            return _context.Products.ToList();
        }

        public Product GetProductById(int id)
        {
            return _context.Products.FirstOrDefault(p => p.Id == id);
        }

        public void AddProduct(Product product)
        {
            if (product == null)
            {
                throw new ArgumentNullException(nameof(product));
            }

            _context.Products.Add(product);
            _context.SaveChanges();
        }

        public void UpdateProduct(Product product)
        {
            if (product == null)
            {
                throw new ArgumentNullException(nameof(product));
            }

            _context.Entry(product).State = EntityState.Modified;
            _context.SaveChanges();
        }

        public void DeleteProduct(int id)
        {
            var product = _context.Products.Find(id);
            if (product == null)
            {
                throw new ArgumentNullException(nameof(product));
            }

            _context.Products.Remove(product);
            _context.SaveChanges();
        }
    }
}
```

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

6단계. 다양한 작업을 수행하는 데 사용되는 다른 액션 메서드를 사용하여 새 제품 컨트롤러를 생성합니다. 이후 같은 것을 호출하는 프런트엔드 애플리케이션을 사용하여 여러 작업을 수행합니다.

```js
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using ProductManagementAPI.Entities;
using ProductManagementAPI.Repositories;

namespace ProductManagementAPI.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class ProductController : ControllerBase
    {
        private readonly IProductRepository _productRepository;

        public ProductController(IProductRepository productRepository)
        {
            _productRepository = productRepository;
        }

        [HttpGet]
        public IActionResult GetAllProducts()
        {
            var products = _productRepository.GetAllProducts();
            return Ok(products);
        }

        [HttpGet("{id}")]
        public IActionResult GetProductById(int id)
        {
            var product = _productRepository.GetProductById(id);
            if (product == null)
            {
                return NotFound();
            }
            return Ok(product);
        }

        [HttpPost]
        public IActionResult AddProduct([FromBody] Product product)
        {
            if (product == null)
            {
                return BadRequest();
            }

            _productRepository.AddProduct(product);
            return CreatedAtAction(nameof(GetProductById), new { id = product.Id }, product);
        }

        [HttpPut("{id}")]
        public IActionResult UpdateProduct(int id, [FromBody] Product product)
        {
            if (product == null || id != product.Id)
            {
                return BadRequest();
            }

            var existingProduct = _productRepository.GetProductById(id);
            if (existingProduct == null)
            {
                return NotFound();
            }

            _productRepository.UpdateProduct(product);
            return NoContent();
        }

        [HttpDelete("{id}")]
        public IActionResult DeleteProduct(int id)
        {
            var existingProduct = _productRepository.GetProductById(id);
            if (existingProduct == null)
            {
                return NotFound();
            }

            _productRepository.DeleteProduct(id);
            return NoContent();
        }
    }
}
```

7단계. 앱 설정 파일을 열고 데이터베이스 연결 문자열을 추가합니다.

```js
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*",
  "ConnectionStrings": {
    "DefaultConnection": "Data Source=DESKTOP-8RL8JOG;Initial Catalog=ReactNetCoreCrudDb;User Id=sa;Password=database@1;"
  }
}
```

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

**단계 8.** 서비스 컨테이너 내에서 서비스를 등록하고 미들웨어를 구성하세요.

```js
using ProductManagementAPI.Data;
using ProductManagementAPI.Repositories;

var builder = WebApplication.CreateBuilder(args);

// 컨테이너에 서비스 추가.
builder.Services.AddScoped<IProductRepository, ProductRepository>();
builder.Services.AddDbContext<AppDbContext>();
builder.Services.AddCors(options => {
    options.AddPolicy("CORSPolicy", builder => builder.AllowAnyOrigin().AllowAnyMethod().AllowAnyHeader());
});


builder.Services.AddControllers();
// Swagger/OpenAPI를 구성하는 방법에 대해 자세히 알아보려면 https://aka.ms/aspnetcore/swashbuckle을 방문하십시오.
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();

// HTTP 요청 파이프라인을 구성하세요.
app.UseCors("CORSPolicy");

if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();

app.UseAuthorization();

app.MapControllers();

app.Run();
```

**단계 9.** 다음 엔티티 프레임워크 데이터베이스 마이그레이션 명령을 실행하여 데이터베이스 및 테이블을 생성하세요.

```js
add-migration “v1”

update-database
```

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

10단계. 마지막으로 애플리케이션을 실행하고 Swagger UI를 사용하여 다양한 API 엔드포인트를 실행합니다.

![image](/assets/img/2024-07-01-BuildandContainerizeProductApplicationusingReactJSNETCoreandDocker_2.png)

# 샘플 제품 애플리케이션: 프론트엔드 (React JS)

React JS를 사용하여 클라이언트 애플리케이션을 만들고 위의 API 엔드포인트를 사용하여 소비합시다.

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

**단계 1.** 다음 명령을 사용하여 새로운 React JS 애플리케이션을 생성하세요:

```js
npx create-react-app react-netcore-crud-app
```

**단계 2.** 프로젝트 디렉토리로 이동하세요:

```js
cd react-netcore-crud-app
```

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

단계 3. 백엔드 API를 소비하고 호출하기 위해 Axios를 설치하고 설계 목적으로 부트스트랩을 설치하세요.

```js
npm install axios
```

```js
npm install bootstrap
```

단계 4. 다음 컴포넌트 및 서비스를 추가하세요.

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

제품 목록 컴포넌트입니다.

```js
// src/components/ProductList/ProductList.js
import React, { useState, useEffect } from 'react';
import ProductListItem from './ProductListItem';
import productService from '../../services/productService';

const ProductList = () => {
    const [products, setProducts] = useState([]);

    useEffect(() => {
        fetchProducts();
    }, []);

    const fetchProducts = async () => {
        try {
            const productsData = await productService.getAllProducts();
            setProducts(productsData);
        } catch (error) {
            console.error('제품을 불러오는 중 오류가 발생했습니다:', error);
        }
    };

    const handleDelete = async (id) => {
        try {
            await productService.deleteProduct(id);
            fetchProducts(); // 제품 목록 새로고침
        } catch (error) {
            console.error('제품 삭제 중 오류가 발생했습니다:', error);
        }
    };

    const handleEdit = () => {
        fetchProducts(); // 편집 후 제품 목록 새로고침
    };

    return (
        <div className="container">
            <h2 className="my-4">제품 목록</h2>
            <ul className="list-group">
                {products.map(product => (
                    <ProductListItem key={product.id} product={product} onDelete={() => handleDelete(product.id)} onEdit={handleEdit} />
                ))}
            </ul>
        </div>
    );
};

export default ProductList;
```

제품 목록 아이템 컴포넌트입니다.

```js
// src/components/ProductList/ProductListItem.js
import React, { useState } from 'react';
import productService from '../../services/productService';

const ProductListItem = ({ product, onDelete, onEdit }) => {
    const [isEditing, setIsEditing] = useState(false);
    const [editedName, setEditedName] = useState(product.name);
    const [editedPrice, setEditedPrice] = useState(product.price);

    const handleEdit = async () => {
        setIsEditing(true);
    };

    const handleSave = async () => {
        const editedProduct = { ...product, name: editedName, price: parseFloat(editedPrice) };
        try {
            await productService.updateProduct(product.id, editedProduct);
            setIsEditing(false);
            onEdit(); // 제품 목록 새로고침
        } catch (error) {
            console.error('제품 업데이트 중 오류가 발생했습니다:', error);
        }
    };

    const handleCancel = () => {
        setIsEditing(false);
        // 수정된 값 초기화
        setEditedName(product.name);
        setEditedPrice(product.price);
    };

    return (
        <li className="list-group-item">
            {isEditing ? (
                <div className="row">
                    <div className="col">
                        <input type="text" className="form-control" value={editedName} onChange={e => setEditedName(e.target.value)} required />
                    </div>
                    <div className="col">
                        <input type="number" className="form-control" value={editedPrice} onChange={e => setEditedPrice(e.target.value)} required />
                    </div>
                    <div className="col-auto">
                        <button className="btn btn-success me-2" onClick={handleSave}>저장</button>
                        <button className="btn btn-secondary" onClick={handleCancel}>취소</button>
                    </div>
                </div>
            ) : (
                <div className="d-flex justify-content-between align-items-center">
                    <span>{product.name} - ${product.price}</span>
                    <div>
                        <button className="btn btn-danger me-2" onClick={onDelete}>삭제</button>
                        <button className="btn btn-primary" onClick={handleEdit}>편집</button>
                    </div>
                </div>
            )}
        </li>
    );
};

export default ProductListItem;
```

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

제품 서비스.

```js
// src/services/productService.js
import axios from 'axios';

const baseURL = 'https://localhost:7202/api/Product';

const productService = {
    getAllProducts: async () => {
        const response = await axios.get(baseURL);
        return response.data;
    },
    addProduct: async (product) => {
        const response = await axios.post(baseURL, product);
        return response.data;
    },
    deleteProduct: async (id) => {
        const response = await axios.delete(`${baseURL}/${id}`);
        return response.data;
    },
    updateProduct: async (id, product) => {
        const response = await axios.put(`${baseURL}/${id}`, product);
        return response.data;
    }
};

export default productService;
```

앱 컴포넌트.

```js
// src/App.js
import React, { useState } from 'react';
import ProductList from './components/ProductList/ProductList';
import ProductForm from './components/ProductForm/ProductForm';

function App() {
    const [refresh, setRefresh] = useState(false);

    const handleProductAdded = () => {
        setRefresh(!refresh); // 다시 렌더링을 트리거 할 수 있도록 새로 고침 상태를 토글
    };

    return (
        <div>
            <ProductList key={refresh} />
            <ProductForm onProductAdded={handleProductAdded} />
        </div>
    );
}

export default App;
```

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

다음 명령을 사용하여 애플리케이션을 실행하고 동일한 도움으로 다양한 CRUD 작업을 수행하세요.

![애플리케이션 실행](/assets/img/2024-07-01-BuildandContainerizeProductApplicationusingReactJSNETCoreandDocker_3.png)

# 애플리케이션을 위한 Docker 파일

- 백앤드 애플리케이션을 위한 Docker 파일 (.NET Core)

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

```js
# 공식 .NET Core SDK를 부모 이미지로 사용합니다.
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build

WORKDIR /app

# 프로젝트 파일을 복사하고 종속성을 복원합니다 (프로젝트 이름에 .csproj를 사용합니다).
COPY *.csproj ./

RUN dotnet restore

# 나머지 응용 프로그램 코드를 복사합니다.
COPY . .

# 응용 프로그램을 게시합니다.
RUN dotnet publish -c Release -o out

# 런타임 이미지를 빌드합니다.
FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS runtime

WORKDIR /app
COPY --from=build /app/out ./

# 응용 프로그램이 실행될 포트를 노출합니다.
EXPOSE 80

# 응용 프로그램을 시작합니다.
ENTRYPOINT ["dotnet", "ProductManagementAPI.dll"]
```

- 1–2번 라인: 공식 .NET Core SDK 이미지 (mcr.microsoft.com/dotnet/sdk:6.0)를 기본 이미지로 사용합니다.
- 4번 라인: 작업 디렉터리를 /app으로 설정합니다.
- 6–7번 라인: 프로젝트 파일(*.csproj)을 컨테이너로 복사합니다.
- 9번 라인: dotnet restore를 실행하여 프로젝트 파일에서 지정된 종속성을 복원합니다.
- 11–12번 라인: 나머지 응용 프로그램 코드를 컨테이너로 복사합니다.
- 14–15번 라인: 응용 프로그램을 릴리스 구성으로 게시합니다 (dotnet publish -c Release -o out) out 디렉터리로 출력합니다.
- 17–18번 라인: 공식 .NET Core ASP.NET 런타임 이미지 (mcr.microsoft.com/dotnet/aspnet:6.0)를 기본 이미지로 사용합니다.
- 20–21번 라인: 작업 디렉터리를 /app으로 설정하고 빌드 단계의 게시된 출력(from /app/out)을 런타임 단계의 /app 디렉터리로 복사합니다.
- 23–24번 라인: 외부에서 응용 프로그램에 액세스할 수 있도록 포트 80을 노출합니다.
- 26–27번 라인: 응용 프로그램을 시작하는 엔트리 포인트 명령어로 dotnet ProductManagementAPI.dll을 지정합니다.

2. 프론트엔드 애플리케이션 (React JS)을 위한 Docker 파일

```js
# 공식 Node.js 기본 이미지 사용
FROM node:18

# 작업 디렉터리 설정
WORKDIR /app

# package.json 및 package-lock.json 파일 복사
COPY package*.json ./

# 종속성 설치
RUN npm install

# 나머지 애플리케이션 코드 복사
COPY . .

# React 앱 빌드
ARG REACT_APP_API_URL
ENV REACT_APP_API_URL=$REACT_APP_API_URL
RUN npm run build

# serve를 전역적으로 설치하여 빌드 폴더를 제공합니다
RUN npm install -g serve

# 앱이 실행되는 포트 노출
EXPOSE 3000

# React 앱 시작
CMD ["serve", "-s", "build"]
```

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

- 1–2번째 줄: Node.js 버전 18을 사용하는 기본 이미지를 지정합니다.
- 4–5번째 줄: 이후 명령에 대한 작업 디렉토리로 /app를 설정합니다.
- 7–8번째 줄: package.json 및 package-lock.json을 로컬 머신에서 Docker 이미지로 복사합니다.
- 10–11번째 줄: package.json에 나열된 모든 종속성을 설치하기 위해 npm install을 실행합니다.
- 13–14번째 줄: 나머지 애플리케이션 코드를 Docker 이미지로 복사합니다.
- 16–19번째 줄: ARG 명령은 빌드 시간 변수 REACT_APP_API_URL을 정의합니다. ENV 명령은 빌드 시간 변수의 값인 REACT_APP_API_URL 환경 변수를 설정합니다. 또한 RUN npm run build는 React 애플리케이션을 빌드합니다.
- 21–22번째 줄: serve 패키지를 전역으로 설치하여 빌드된 React 애플리케이션을 제공합니다.
- 24–25번째 줄: 애플리케이션이 실행될 포트인 3000을 노출합니다.
- 27–28번째 줄: 명령은 serve를 사용하여 빌드 폴더를 제공함으로써 React 애플리케이션을 시작합니다.

프런트엔드 애플리케이션에 대해 React 프로젝트 루트에 .env 파일을 생성합니다. 이 파일에는 환경 변수가 포함되어 있으며, Docker 이미지를 실행하거나 백엔드 API URL을 전달하는 데 사용할 수 있습니다.

```js
REACT_APP_API_URL=http://your-backend-url.com
```

다음으로, 제품 서비스에서 백엔드 하드코딩된 URL을 수정하십시오.

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

```js
// src/services/productService.js
import axios from 'axios';

const baseURL = process.env.REACT_APP_API_URL;

const productService = {
    getAllProducts: async () => {
        const response = await axios.get(baseURL);
        return response.data;
    },
    addProduct: async (product) => {
        const response = await axios.post(baseURL, product);
        return response.data;
    },
    deleteProduct: async (id) => {
        const response = await axios.delete(`${baseURL}/${id}`);
        return response.data;
    },
    updateProduct: async (id, product) => {
        const response = await axios.put(`${baseURL}/${id}`, product);
        return response.data;
    }
};

export default productService;
```

## 프론트엔드 및 백엔드 어플리케이션을 컨테이너화하기

단계 1.

도커 이미지 빌드하기


<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

```js
도커 빌드 명령어를 사용하여 productbackendapp:latest 이미지를 빌드하세요.
```

![이미지](/assets/img/2024-07-01-BuildandContainerizeProductApplicationusingReactJSNETCoreandDocker_4.png)

```js
도커 빌드 명령어를 사용하여 REACT_APP_API_URL이 http://localhost:8085/api/Product인 productfrontendapp 이미지를 빌드하세요.
```

![이미지](/assets/img/2024-07-01-BuildandContainerizeProductApplicationusingReactJSNETCoreandDocker_5.png)


<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

Step 2.

필요한 매개변수를 전달한 후 이미지를 실행합니다. 필요한 매개변수는 env와 arguments입니다.

```js
docker run -p 8085:80 -e "ConnectionStrings__DefaultConnection=Data Source=192.168.100.194,1433;Initial Catalog=ReactNetCoreCrudDb;User Id=sa;Password=database@1;" productbackendapp:latest
```

![이미지](/assets/img/2024-07-01-BuildandContainerizeProductApplicationusingReactJSNETCoreandDocker_6.png)

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>


![Image 7](/assets/img/2024-07-01-BuildandContainerizeProductApplicationusingReactJSNETCoreandDocker_7.png)

![Image 8](/assets/img/2024-07-01-BuildandContainerizeProductApplicationusingReactJSNETCoreandDocker_8.png)

![Image 9](/assets/img/2024-07-01-BuildandContainerizeProductApplicationusingReactJSNETCoreandDocker_9.png)

![Image 10](/assets/img/2024-07-01-BuildandContainerizeProductApplicationusingReactJSNETCoreandDocker_10.png)


<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

```js
도커 실행 -p 3000:3000 productfrontendapp
```

![이미지1](/assets/img/2024-07-01-BuildandContainerizeProductApplicationusingReactJSNETCoreandDocker_11.png)

![이미지2](/assets/img/2024-07-01-BuildandContainerizeProductApplicationusingReactJSNETCoreandDocker_12.png)

![이미지3](/assets/img/2024-07-01-BuildandContainerizeProductApplicationusingReactJSNETCoreandDocker_13.png)


<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

# 샘플 애플리케이션 스크린샷

![screenshot1](/assets/img/2024-07-01-BuildandContainerizeProductApplicationusingReactJSNETCoreandDocker_14.png)

![screenshot2](/assets/img/2024-07-01-BuildandContainerizeProductApplicationusingReactJSNETCoreandDocker_15.png)

![screenshot3](/assets/img/2024-07-01-BuildandContainerizeProductApplicationusingReactJSNETCoreandDocker_16.png)

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>


![이미지 1](/assets/img/2024-07-01-BuildandContainerizeProductApplicationusingReactJSNETCoreandDocker_17.png)

![이미지 2](/assets/img/2024-07-01-BuildandContainerizeProductApplicationusingReactJSNETCoreandDocker_18.png)

# GitHub

https://github.com/Jaydeep-007/React_NETCore_CRUD-Docker


<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

# 결론

이 기사에서는 .NET Core 및 SQL Server를 사용하여 제품 관리 백엔드 애플리케이션을 만들었습니다. 이 애플리케이션은 CRUD 작업을 수행하는 데 필요한 다양한 API 엔드포인트를 사용합니다. 나중에 React JS를 사용하여 프론트엔드 애플리케이션을 만들고, Axios를 활용하여 백엔드 애플리케이션을 소비하였습니다. 또한, 두 애플리케이션을 Docker를 사용하여 컨테이너화하였습니다.