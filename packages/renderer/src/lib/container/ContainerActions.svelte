<script lang="ts">
import { faEllipsisVertical, faFileCode, faPlayCircle, faRocket, faTerminal } from '@fortawesome/free-solid-svg-icons';
import { faStopCircle } from '@fortawesome/free-solid-svg-icons';
import { faArrowsRotate } from '@fortawesome/free-solid-svg-icons';
import { faTrash } from '@fortawesome/free-solid-svg-icons';
import { faExternalLinkSquareAlt } from '@fortawesome/free-solid-svg-icons';
import { ContainerGroupInfoTypeUI, ContainerInfoUI } from './ContainerInfoUI';
import Fa from 'svelte-fa/src/fa.svelte';
import { router } from 'tinro';
import ListItemButtonIcon from '../ui/ListItemButtonIcon.svelte';
import DropdownMenu from '../ui/DropdownMenu.svelte';
import FlatMenu from '../ui/FlatMenu.svelte';
export let container: ContainerInfoUI;
export let backgroundColor = 'bg-zinc-800';
export let dropdownMenu: boolean = false;

async function startContainer(containerInfo: ContainerInfoUI) {
  await window.startContainer(containerInfo.engineId, containerInfo.id);
}

async function restartContainer(containerInfo: ContainerInfoUI) {
  await window.restartContainer(containerInfo.engineId, containerInfo.id);
}

async function stopContainer(containerInfo: ContainerInfoUI) {
  await window.stopContainer(containerInfo.engineId, containerInfo.id);
}
function openBrowser(containerInfo: ContainerInfoUI): void {
  window.openExternal(containerInfo.openingUrl);
}

async function deleteContainer(containerInfo: ContainerInfoUI): Promise<void> {
  await window.deleteContainer(containerInfo.engineId, containerInfo.id);
  router.goto('/containers/');
}
function openTerminalContainer(containerInfo: ContainerInfoUI): void {
  router.goto(`/containers/${container.id}/terminal`);
}

function openGenerateKube(): void {
  router.goto(`/containers/${container.id}/kube`);
}

function deployToKubernetes(): void {
  router.goto(`/deploy-to-kube/${container.id}/${container.engineId}`);
}

// If dropdownMenu = true, we'll change style to the imported dropdownMenu style
// otherwise, leave blank.
let actionsStyle;
if (dropdownMenu) {
  actionsStyle = DropdownMenu;
} else {
  actionsStyle = FlatMenu;
}
</script>

<ListItemButtonIcon
  title="Start Container"
  onClick="{() => startContainer(container)}"
  hidden="{container.state === 'RUNNING'}"
  backgroundColor="{backgroundColor}"
  icon="{faPlayCircle}" />

<ListItemButtonIcon
  title="Stop Container"
  onClick="{() => stopContainer(container)}"
  hidden="{!(container.state === 'RUNNING')}"
  backgroundColor="{backgroundColor}"
  icon="{faStopCircle}" />

<ListItemButtonIcon
  title="Delete Container"
  onClick="{() => deleteContainer(container)}"
  backgroundColor="{backgroundColor}"
  icon="{faTrash}" />

<!-- If dropdownMenu is true, use it, otherwise just show the regular buttons -->
<svelte:component this="{actionsStyle}" backgroundColor="{backgroundColor}">
  <ListItemButtonIcon
    title="Generate Kube"
    onClick="{() => openGenerateKube()}"
    menu="{dropdownMenu}"
    hidden="{!(container.engineType === 'podman' && container.groupInfo.type === ContainerGroupInfoTypeUI.STANDALONE)}"
    backgroundColor="{backgroundColor}"
    icon="{faFileCode}" />
  <ListItemButtonIcon
    title="Deploy to Kubernetes"
    onClick="{() => deployToKubernetes()}"
    menu="{dropdownMenu}"
    hidden="{!(container.engineType === 'podman' && container.groupInfo.type === ContainerGroupInfoTypeUI.STANDALONE)}"
    backgroundColor="{backgroundColor}"
    icon="{faRocket}" />
  <ListItemButtonIcon
    title="Open Browser"
    onClick="{() => openBrowser(container)}"
    menu="{dropdownMenu}"
    hidden="{!(container.state === 'RUNNING' && container.hasPublicPort)}"
    backgroundColor="{backgroundColor}"
    icon="{faExternalLinkSquareAlt}" />
  <ListItemButtonIcon
    title="Open Terminal"
    onClick="{() => openTerminalContainer(container)}"
    menu="{dropdownMenu}"
    hidden="{!(container.state === 'RUNNING')}"
    backgroundColor="{backgroundColor}"
    icon="{faTerminal}" />
  <ListItemButtonIcon
    title="Restart Container"
    onClick="{() => restartContainer(container)}"
    menu="{dropdownMenu}"
    backgroundColor="{backgroundColor}"
    icon="{faArrowsRotate}" />
</svelte:component>
