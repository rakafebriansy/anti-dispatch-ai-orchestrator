# API Endpoint: `[METHOD] /path/to/endpoint`

**Deskripsi Singkat:** 
[Penjelasan singkat fungsi dari endpoint ini]

## Request Specification

### HTTP Method
`GET` / `POST` / `PUT` / `PATCH` / `DELETE`

### Headers
| Key | Type | Required | Description |
| :--- | :--- | :---: | :--- |
| `Authorization` | `string` | Ya | Bearer token format: `Bearer <token>` |
| `Content-Type` | `string` | Ya | Wajib diset menjadi `application/json` |

### Query Parameters / URL Parameters
| Parameter | Type | Required | Description |
| :--- | :--- | :---: | :--- |
| `id` | `string` | Ya | ID unik dari entitas |
| `limit` | `integer`| Tidak | Batas paginasi (default: 10) |

### Request Body
```json
{
  "field_name": "value",
  "is_active": true
}
```

---

## Response Specification

### Success Response (200 OK / 201 Created)
```json
{
  "status": "success",
  "data": {
    "id": "123",
    "field_name": "value"
  }
}
```

### Error Responses

**400 Bad Request**
```json
{
  "status": "error",
  "message": "Validasi gagal: field_name tidak boleh kosong"
}
```

**401 Unauthorized**
```json
{
  "status": "error",
  "message": "Token tidak valid atau telah kadaluarsa"
}
```

<!-- Catatan Git: Saat melakukan commit/PR untuk dokumen ini, patuhi aturan istilah "orchestrator" (Dilarang di Project/Node, Wajib di Orchestrator). -->
