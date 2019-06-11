var datos;
/**
 * Clase registrar Cuentas
 * @author Jhon Aguiar M
 * */
class RegistroCuenta {

    listaClientes = '';

    datoAvance = '';

    constructor() {
        this.eventos();
        this.listarEjecutivos();
        this.listarTipoCuenta();
        this.listarPortafolio();
        this.listarCliente();
        this.listarTipoContrato();
        this.listarTipoTopologia();
        this.listarFuenteOportunidad();
        this.listarModalidadEntrega();
        this.cargarDatosGrupo();
    }

    eventos() {
        $(".consulta").on("click", () => {
            this.validarBusqueda();
        });

        $("#btnBuscarResponsable").on("click", () => {
            this.buscarResponsable();
        });

        $("#btnBuscarCliente").on("click", () => {
            this.buscarCliente();
        });

        $("#enviarRegistro").on("submit", (e) => {
            e.preventDefault();
            this.guardarForm();
        });

        $(document).on("click", ".editar-cliente", (e) => {
            this.completarInfo(e.target.id);
        });

        $(document).on("click", ".modificar-porcentaje", (e) => {
            this.agregarElemento;
        });

        $(document).on("click", ".cambiar-estado", (e) => {
            datos = e.target.id;
        });

        $(document).on("click", ".grabar-element", (e) => {
            this.guardarDatosGrupo(e.target.dataset.grupo);
        });

    }


    guardarDatosGrupo(idGrupo) {
        this.postRequest2(UrlApi + '/api/Comercial', idGrupo)
            .then(d => this.validacionCodigo(d)) // Result from the `response.json()` call
            .catch(error => console.error(error));
    }

    postRequest2(url, idGrupo) {
        return fetch(url + '?idGrupo=' + idGrupo+'&idCuenta='+datos, {
            method: 'GET',
            headers: new Headers({
                'Content-Type': 'application/json'
            })
        }).then(response => response.json());
    }

    llenarDatosGrupo(result) {
        const fresult = [];
        const map = new Map();
        for (const item of result) {
            if (!map.has(item.grupoCriterio.idGrupo)) {
                map.set(item.grupoCriterio.idGrupo, true);    // set any value to Map
                fresult.push({
                    idGrupo: item.grupoCriterio.idGrupo,
                    nombre: item.grupoCriterio.nombre,
                    porcentaje: item.grupoCriterio.porcentaje
                });
            }
        }

        fresult.forEach((v, i) => {
            let y = '';
            for (let x = 0; x < result.length; x++) {
                if (result[x].grupoCriterio.idGrupo === v.idGrupo) {
                    y += `<li>${result[x].descripcion}</li>`;
                }
            }

            document.querySelector("#tabla-cuentas").innerHTML += `<div class="panel panel-default">
                <div class="panel-heading" role="tab" id="heading${i}">
                    <h4 class="panel-title">
                        <a role="button" class="collapsed" data-toggle="collapse" data-parent="#accordion" role="tabpanel" href="#collapse${i}" aria-expanded="true" aria-controls="collapse${i}">
                        ${v.nombre} - ${v.porcentaje} % </a>
                    </h4>
                </div>
                <div id="collapse${i}" class="panel-collapse collapse" role="tabpanel" aria-labelledby="heading${i}">
                    <div class="panel-body">
                        <ul>${y}</ul>
                        <hr>
                        <button class="btn btn-success grabar-element" data-grupo="${v.idGrupo}">Grabar</button>
                    </div>
                </div>
            </div>`;
            
        });
    }

    cargarDatosGrupo() {
        fetch(UrlApi + '/api/Comercial?filtro=ConsultarGrupos&IdProyecto='+ '0')
            .then(UniversalHelper.handleResponse)
            .then(d => this.llenarDatosGrupo(d))
            .catch(error => console.error(error));
    }

    llenarInfo(data) {
        $("#registro").click();
        document.querySelector("#idCuenta").value = data.idCuenta;
        document.querySelector("#idEjecutivoCuenta").value = data.idEjecutivoCuenta;
        document.querySelector("#idCliente").value = data.idCliente;
        document.querySelector("#idTipoCuenta").value = data.idTipoCuenta;
        document.querySelector("#idTipoContrato").value = data.idTipoContrato;
        document.querySelector("#idPortafolio").value = data.idPortafolio;
        document.querySelector("#detalle").value = data.detalle;
        document.querySelector("#oportunidad").checked = data.oportunidad;
        document.querySelector("#requerimientoIng").checked = data.requerimientoIng;
        document.querySelector("#idTipologia").value = data.idTipoTopologia;
        document.querySelector("#presupuestoCliente").value = data.presupuestoCliente;
        document.querySelector("#precioColvatel").value = data.precioColvatel;
        document.querySelector("#consecutivoSalida").value = data.consecutivoSalida;
        document.querySelector("#idGrupo").value = data.grupoCriterio.porcentaje;
        document.querySelector("#plazoEjecucion").value = data.plazoEjecucion;
        document.querySelector("#idFuenteOportunidad").value = data.idFuenteOportunidad;
        document.querySelector("#idModalidadEntrega").value = data.idModalidadEntrega;
        document.querySelector("#observaciones").value = data.observaciones;
    }

    completarInfo(id) {
        fetch(UrlApi + '/api/Comercial?filtro=ConsultarCuenta&IdProyecto=' + id )
            .then(UniversalHelper.handleResponse)
            .then(d => { this.llenarInfo(d[0]); })
            .then().catch(error => console.error(error));
    }

    guardarForm() {
        this.postRequest(UrlApi + '/api/Comercial/RegistrarCuenta')
            .then(d => this.validacionCodigo(d)) // Result from the `response.json()` call
            .catch(error => console.error(error));
    }

    postRequest(url, data) {
        return fetch(url, {
            credentials: 'same-origin', // 'include', default: 'omit'
            method: 'POST',
            body:  this.objetoRegistro(), // Coordinate the body type with 'Content-Type'
            headers: new Headers({
                'Content-Type': 'application/json'
            })
        }).then(response => response.json());
    }

    objetoRegistro() {
        return JSON.stringify({
            idCuenta: document.querySelector("#idCuenta").value,
            oportunidad: document.querySelector("#oportunidad").checked ,
            detalle: document.querySelector("#detalle").value ,
            requerimientoIng: document.querySelector("#requerimientoIng").checked ,
            presupuestoCliente: document.querySelector("#presupuestoCliente").value ,
            precioColvatel: document.querySelector("#precioColvatel").value ,
            consecutivoSalida: document.querySelector("#consecutivoSalida").value ,
            idGrupo: 7 ,
            plazoEjecucion: document.querySelector("#plazoEjecucion").value ,
            observaciones: document.querySelector("#observaciones").value ,
            estado: true ,
            idEjecutivoCuenta: document.querySelector("#idEjecutivoCuenta").value,
            idCliente: document.querySelector("#idCliente").value ,
            idTipoCuenta: document.querySelector("#idTipoCuenta").value ,
            idPortafolio: document.querySelector("#idPortafolio").value ,
            idTipoContrato: document.querySelector("#idTipoContrato").value ,
            idTipoTopologia: document.querySelector("#idTipologia").value ,
            idFuenteOportunidad: document.querySelector("#idFuenteOportunidad").value ,
            idModalidadEntrega: document.querySelector("#idModalidadEntrega").value,
            usuarioRegistra: UserId
        });
    }


    buscarResponsable() {
        let idResponsable = $("#buscar-responsable").val();
        fetch(UrlApi + '/api/Comercial?filtro=BuscarResponsable&IdProyecto=' + idResponsable)
            .then(UniversalHelper.handleResponse)
            .then(d => { this.completarTabla(d); })
            .catch(error => console.error(error));
    }

    buscarCliente() {
        let idCliente = $("#buscar-cliente").val();
        fetch(UrlApi + '/api/Comercial?filtro=BucarCliente&IdProyecto=' + idCliente)
            .then(UniversalHelper.handleResponse)
            .then(d => { this.completarTabla(d); })
            .catch(error => console.error(error));
    }
    
    validarBusqueda() {
        if ($("input[type='radio'].consulta").is(':checked')) {
            let idBusqueda = $("input[type='radio'].consulta:checked").val();
            this.criterioBusqueda(idBusqueda);
        }
    }

    criterioBusqueda(id) {
        if (id === "1") {
            $("#select-responsable").css("display", 'block');
            $("#select-cliente").css("display", 'none');
        } else {
            $("#select-cliente").css("display", 'block');
            $("#select-responsable").css("display", 'none');
        }
    }

    completarTabla(result) {
        this.listaClientes = result;
        $("#tabla-busqueda").show();
        $("#tablaBusqueda").DataTable({
            destroy: true,
            data: result,
            columns: [
                { title: "Responsable", data: "idEjecutivoCuenta" },
                { title: "Cliente", data: "cliente.Nombre" },
                {
                    title: "Oportunidad",
                    minwidth: 60,
                    bSortable: false,
                    mRender: function (i, type, row, fila) {
                        var bool = row.oportunidad === true ? "SI" : "NO";
                        return bool;
                    }
                },
                { title: "Detalle", data: "detalle" },
                {
                    title: "Porcentaje",
                    minwidth: 60,
                    bSortable: false,
                    mRender: function (i, type, row, fila) {
                        return row.grupoCriterio.porcentaje+"%";
                    }
                },
                {
                    title: "Acciones",
                    minwidth: 60,
                    bSortable: false,
                    mRender: function (i, type, row, fila) {
                        return `
                            <button id='${row.idCuenta}' type='button' class='btn btn-info editar-cliente'>Editar</button>
                            <button id='${row.idCuenta}' type='button' class='btn btn-success cambiar-estado' data-toggle="modal" data-target="#myModal">Avance</button>
                        `;
                    }
                }
            ],
            bPaginate: true,
            bFilter: true,
            bInfo: true,
            responsive: true
        });
    }

    listarEjecutivos() {
        fetch('/Account/GetUsersByArea?area=COMERCIAL')
            .then(UniversalHelper.handleResponse)
            .then(async d => await this.completarDataEjecutivoCuenta(d).then($("#idEjecutivoCuenta").prop("disabled", false)))
            .catch(error => console.error(error));
    }

    async completarDataEjecutivoCuenta(data) {
        document.querySelector("#idEjecutivoCuenta").innerHTML = '<option value="" selected disabled>-- Seleccione una opción --</option>';
        document.querySelector("#buscar-responsable").innerHTML = '<option value="" selected disabled>-- Seleccione una opción --</option>';
        data.forEach((v, i) => {
            document.querySelector("#idEjecutivoCuenta").innerHTML += '<option value="' + v.Cedula + '">' + v.Nombre + '</option>';
            document.querySelector("#buscar-responsable").innerHTML += '<option value="' + v.Cedula + '">' + v.Nombre + '</option>';
        });
    }

    listarTipoCuenta() {
        fetch(UrlApi + '/api/Comercial?filtro=TipoCuenta')
            .then(UniversalHelper.handleResponse)
            .then(async d => await this.completarDataTipoCuenta(d).then($("#idTipoCuenta").prop("disabled", false)))
            .catch(error => console.error(error));
    }

    async completarDataTipoCuenta(data) {
        document.querySelector("#idTipoCuenta").innerHTML = '<option value="" selected disabled>-- Seleccione una opción --</option>';
        data.forEach((v, i) => {
            document.querySelector("#idTipoCuenta").innerHTML += '<option value="' + v.idTipoCuenta + '">' + v.nombre + '</option>';
        });
    }

    listarPortafolio() {
        fetch(UrlApi + '/api/Comercial?filtro=Portafolio')
            .then(UniversalHelper.handleResponse)
            .then(async d => await this.completarDataPortafolio(d).then($("#idPortafolio").prop("disabled", false)))
            .catch(error => console.error(error));
    }

    async   completarDataPortafolio(data) {
        document.querySelector("#idPortafolio").innerHTML = '<option value="" selected disabled>-- Seleccione una opción --</option>';
        data.forEach((v, i) => {
            document.querySelector("#idPortafolio").innerHTML += '<option value="' + v.idPortafolio + '">' + v.nombre + '</option>';
        });
    }

    listarCliente() {
        fetch(UrlApi + '/api/Comercial?filtro=Cliente')
            .then(UniversalHelper.handleResponse)
            .then(async d => await this.completarDataCliente(d).then($("#idCliente").prop("disabled", false)))
            .catch(error => console.error(error));
    }

    async completarDataCliente(data) {
        document.querySelector("#idCliente").innerHTML = '<option value="" selected disabled>-- Seleccione una opción --</option>';
        document.querySelector("#buscar-cliente").innerHTML = '<option value="" selected disabled>-- Seleccione una opción --</option>';
        data.forEach((v, i) => {
            document.querySelector("#idCliente").innerHTML += '<option value="' + v.idCliente + '">' + v.Nombre + '</option>';
            document.querySelector("#buscar-cliente").innerHTML += '<option value="' + v.idCliente + '">' + v.Nombre + '</option>';
        });
    }

    listarTipoContrato() {
        fetch(UrlApi + '/api/Comercial?filtro=TipoContrato')
            .then(UniversalHelper.handleResponse)
            .then(async d => await this.completarDataTipoContrato(d).then($("#idTipoContrato").prop("disabled", false)))
            .catch(error => console.error(error));
    }

    async completarDataTipoContrato(data) {
        document.querySelector("#idTipoContrato").innerHTML = '<option value="" selected disabled>-- Seleccione una opción --</option>';
        data.forEach((v, i) => {
            document.querySelector("#idTipoContrato").innerHTML += '<option value="' + v.idTipoContrato + '">' + v.nombre + '</option>';
        });
    }

    listarTipoTopologia() {
        fetch(UrlApi + '/api/Comercial?filtro=TipoTopologia')
            .then(UniversalHelper.handleResponse)
            .then(async d => await this.completarDataTipoTopologia(d).then($("#idTipologia").prop("disabled", false)))
            .catch(error => console.error(error));
    }

    async completarDataTipoTopologia(data) {
        document.querySelector("#idTipologia").innerHTML = '<option value="" selected disabled>-- Seleccione una opción --</option>';
        data.forEach((v, i) => {
            document.querySelector("#idTipologia").innerHTML += '<option value="' + v.idTipoTopologia + '">' + v.nombre + '</option>';
        });
    }

    listarFuenteOportunidad() {
        fetch(UrlApi + '/api/Comercial?filtro=FuenteOportunidad')
            .then(UniversalHelper.handleResponse)
            .then(async d => await this.completarDataFuenteOportunidad(d).then($("#idFuenteOportunidad").prop("disabled", false)))
            .catch(error => console.error(error));
    }

    async completarDataFuenteOportunidad(data) {
        document.querySelector("#idFuenteOportunidad").innerHTML = '<option value="" selected disabled>-- Seleccione una opción --</option>';
        data.forEach((v, i) => {
            document.querySelector("#idFuenteOportunidad").innerHTML += '<option value="' + v.IdFuenteOportunidad + '">' + v.Nombre + '</option>';
        });
    }

    listarModalidadEntrega() {
        fetch(UrlApi + '/api/Comercial?filtro=modalidadEntrega')
            .then(UniversalHelper.handleResponse)
            .then(async d => await this.completarDataModalidadEntrega(d).then($("#idModalidadEntrega").prop("disabled", false)))
            .catch(error => console.error(error));
    }

    async completarDataModalidadEntrega(data) {
        document.querySelector("#idModalidadEntrega").innerHTML = '<option value="" selected disabled>-- Seleccione una opción --</option>';
        data.forEach((v, i) => {
            document.querySelector("#idModalidadEntrega").innerHTML += '<option value="' + v.idModalidad + '">' + v.nombre + '</option>';
        });
    }

    // Validacion estado consulta
    validacionCodigo(data) {
        if (data.code === 200) {
            swal({
                title: "Ok",
                text: "Se ha realizado la creacion del cliente con exito",
                type: "success",
                showCancelButton: false,
                confirmButtonText: "Aceptar",
                closeOnConfirm: true
            }, () => {
                bootbox.hideAll();
            });
            location.reload();
        } else {
            swal("Error.!", "Se ha presentado el siguiente error al crear la cuenta " + data.code + ". Mensaje: " + data.description, "error");
        }
    }

}

document.addEventListener("DOMContentLoaded", () => {
    var reg = new RegistroCuenta();
});
